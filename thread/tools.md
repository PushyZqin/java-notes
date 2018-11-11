## 1. 队列

### 1.1 阻塞队列

阻塞队列`BlockingQueue`是一个在队列基础上又支持了两个附加操作的队列。它提供了可阻塞的`put`和`take`方法，以及支持定时的`offset`和`poll`方法：

- `put()`：如果队列已经满了，将阻塞直到有空间可以用；
- `take()`：如果队列为空，将阻塞直到队列变为非空；
- `offset()`和`poll()`与`put()`和`take()`相对应，但是可以设置阻塞的超时时间，一旦过了超时时间，将会返回一个失败的状态（NULL）。

JDK中提供了许多`BlockingQueue`的实现：

- `LinkedBlockingQueue`：由链表结构组成的有界阻塞队列；
- `ArrayBlockingQueue`：由数组结构组成的有界阻塞队列；
- `PriorityBlockingQueue`：支持优先级的无界阻塞队列；

使用`BlockingQueue`很容易地能实现生产者——消费者模式：

```java
// 生产者线程
public class Producer extends Thread {

    private BlockingQueue<Integer> queue;
    public Producer(BlockingQueue<Integer> queue) {
        this.queue = queue;
    }
    
    @Override
    public void run() {
        Random random = new Random();
        System.out.println("生产者开始生产");
        while (!isInterrupted()) {
            try {
                Integer data = random.nextInt();
                Thread.sleep(5000);  // 模拟生产时间
                System.out.println("生成数据 => " + data);
                queue.put(data);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

// 消费者线程
public class Consumer extends Thread {

    private BlockingQueue<Integer> queue;
    public Consumer(BlockingQueue<Integer> queue) {
            this.queue = queue;
    }
    
    @Override
    public void run() {
        while (!isInterrupted()) {
            System.out.println("正从队列获取数据...");
            try {
                Integer data = queue.take();
                System.out.println("成功获取数据.. 开始消费 => " + data);
                Thread.sleep(1000);  // 模拟消费时间
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

然后启动这两个线程：

```java
BlockingQueue<Integer> blockingQueue = new LinkedBlockingDeque<>(2);

new Producer(blockingQueue).start();
new Consumer(blockingQueue).start();
```

结果为：

```java
生产者开始生产
正从队列获取数据...
// 等待5秒，消费者处于阻塞状态
生成数据 => 83913925
成功获取数据.. 开始消费 => 83913925
// 等待1秒，生产者处于阻塞状态
正从队列获取数据...
// 循环...
```

更多内容可以看[解读 Java 并发队列 BlockingQueue](http://www.importnew.com/28053.html)

### 1.2 双端队列

双端队列`Deque`继承自`Queue`，实现了在队列头和队列尾高效地插入和移除。JDK中有以下实现：

- `LinkedList`
- `ArayDeque`
- `LinkedBlockingDeque` 

```java
Deque<Integer> deque = new LinkedBlockingDeque<>();

deque.addFirst(1);
deque.add(2);  // 默认为addLast
deque.addLast(3);

deque.remove(); // 默认为removeFirst
deque.removeLast();
```

双端队列适用于**工作密取模式**，即在工作密取的设计中，每个消费者都有自己的双端队列。如果一个消费者完成了自己双端队列中的全部工作，那么它可以从其他消费者双端队列队尾秘密地获取工作。

这样，**工作者线程不会在单个共享的任务队列上发生竞争**，因为大多数它们都是访问自己的工作队列，极大地减少竞争。而当工作者需要访问另一个队列时，它会从队列的尾部而不是从头部获取工作，因此进一步降低了队列的竞争程度。

## 2. 同步工具类

### 2.1 闭锁

闭锁是一种同步工具类，可以延迟线程的进度知道其到达终止状态。当闭锁到达结束之后，将不会再改变状态。因此**闭锁可以确保某些活动直到其他活动都完成后才继续执行的情况**。

闭锁的作用相当于一扇门：在闭锁到达结束状态之前，这扇门一直是关闭的，并且没有任何线程能通过，当到达结束状态时，这扇门会打开并允许所有的线程通过。

`CountDownLatch`是一钟灵活的闭锁实现，闭锁状态包括一个计数器，该计数器初始化为一个整数，表示需要等待的事件数量。`countDown()`方法递减计数器（表示一个事件已经完成），`await()`方法阻塞等待计数器归零。

```java
public class TestHarness {
    
    private CountDownLatch startGate = new CountDownLatch(1);
    private CountDownLatch endGate = new CountDownLatch(10);
    
    private void run() throws InterruptedException {
        for (int i = 0; i < 3; i++) {
            Thread t = new Thread() {
                @Override
                public void run() {
                    try {
                        System.out.println("线程开始等待执行...");
                        startGate.await(); 
                        // 阻塞，等待闭锁释放，即startGate计数器归零
                        System.out.println("线程执行完毕！");
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    } finally {
                        // 每当一个线程执行完成，都将endGate递减
                        // 这样当所有线程执行完成后，endGate将归零
                        endGate.countDown();
                    }
                }
            };
            t.start();
        }

        long start = System.nanoTime();
        Thread.sleep(2000);  // 等待2秒，模拟初始化过程
        startGate.countDown();  // 当调用startGate的countDown方法后，所有的线程开始启动
        System.out.println("线程开始执行！");
        endGate.await();  // 等待所有的线程执行完成
        System.out.println("所有线程执行完毕！");
        long end = System.nanoTime();

        System.out.println("start - end = " + String.valueOf(end - start) );
    }
}
```

运行结果为：

```java
线程开始等待执行...
线程开始等待执行...
线程开始等待执行...
// 等待2秒，等待startGate归零
线程开始执行！  // 执行startGate::countDown
线程执行完毕！
线程执行完毕！
线程执行完毕！
所有线程执行完毕！  // endGate归零
start - end = 1999872444
```

### 2.2 信号量

计数信号量（Counting Semapphore）**用来控制同时访问某个特定资源的操作数量，或者同时执行某个指定操作的数量**。计数信号量可以用来实现某种资源池，或者对容器施加边界。

`Semapphore`中管理着一组虚拟的许可，在执行操作时可以首先获取许可（只要还有剩余的许可），并在使用之后释放许可，**如果没有许可，那么acquire将阻塞直到有许可**。

例如，我们通过信号量实现一个有界集合，并定义`add()`和`remove()`方法，`add()`方法中当前的元素如果等于信号量，那么插入操作将被阻塞，直到集合的元素小于信号量。

```java
public class BoundedHashSet<T> {
    
    private final Set<T> set;
    private final Semaphore sem;  // 信号量
    
    public BoundedHashSet(int bound) {
        this.set = Collections.synchronizedSet(new HashSet<>());
        this.sem = new Semaphore(bound); // 许可初始量
    }
    
    public boolean add(T o) throws InterruptedException {
        // 如果当前的信号量大于3，那么将阻塞，直到信号量小于3时
        sem.acquire();   // 申请许可证
        boolean wasAdded = false;
        try {
            wasAdded = set.add(o);
            return wasAdded;
        } finally {
            if (!wasAdded) {
                sem.release();  // 操作失败，释放许可证
            }
        }
    }
    
    public boolean remove(Object o) {
        boolean wasRemoved = set.remove(o);
        if (wasRemoved) {
            sem.release();  // 释放许可
        }
        return wasRemoved;
    }
    
}
```

我们可以测试该`BoundedHashSet`类：

```java
BoundedHashSet<Integer> boundedHashSet = new BoundedHashSet<>(3);
boundedHashSet.add(1);
boundedHashSet.add(2);
boundedHashSet.add(3);
// 集合内的元素已经达到信号量，此时调用add方法将被阻塞

// 创建一个线程，在2秒之后移除一个元素（释放一个许可）
new Thread(() -> {
    try {
        Thread.sleep(2000);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
    boundedHashSet.remove(1);

}).start();

boundedHashSet.add(4);
// 等待2秒后，插入成功
```