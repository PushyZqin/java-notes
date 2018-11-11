## 1. Executors框架

**任务是一组逻辑工作单元，而线程则是使任务异步执行的机制**。通常我们有如下的任务处理机制：

- 所有任务在单个线程串行执行（糟糕的响应性和吞吐量）
- 每个任务在各自的线程中执行（资源管理的复杂性）

在Java类中，任务执行的主要抽象不是Thread，而是Executors：

```java
// Executor接口
public interface Executor {
    void execute(Runnable command);
}
```

Executor为异步任务执行框架提供了基础，该框架能支持不同类型的任务执行策略。Executor基于生产者——消费者模式：**提供任务的操作相当于生产者，执行任务的线程相当于消费者**。

## 2. 线程池

线程池，简单来说是**指管理一组同构工作线程的资源池**。线程池与工作队列相关：在工作队列中保存了所有等待的人任务，工作者线程的任务是——从工作队列中获取一个任务，执行任务，然后返回线程池并等待下一个任务。

线程池的好处是**可以重用现有的线程而不是创建新线程**，降低了线程创建和销毁的开销；另外，**不会由于等待创建线程而延迟任务的执行，提高了响应性**。

Executor的静态工厂方法提供了不同任务执行策略的线程池，都可通过`new` + 线程池类型名进行创建：

### 2.1 FixedThreadPool

FixedThreadPool是一个固定长度的线程池，它有如下的特点：

- 可控制线程最大并发数，超出的线程会在队列中等待。
- 阻塞队列采用了`LinkedBlockingQueue`，因此永远不可能拒绝任务；
- `keepAliveTime`为0，意味着一旦有多余的空闲线程，就会被立即停止掉

```java
// 根据系统资源进行设置线程池大小
int nThreads = Runtime.getRuntime().availableProcessors();
// 通过newFixedThreadPool创建该线程池
ExecutorService fixedThreadPool = Executors.newFixedThreadPool(nThreads);

for (int i = 0; i < 10; i++) {
    final int index = i;

    Runnable task = () -> {
        System.out.println(index);
        Thread.sleep(2000);
    };
    // 执行Runnable任务
    fixedThreadPool.execute(task);
}
```

假设`nThreads`为4，那么在循环执行10个任务时，`fixedThreadPool`最多只能执行4个：

```
0
1
2
3
// 等待2秒
4
5
7
6
// 等待2秒
```

### 2.2 CachedThreadPool

CachedThreadPool是一个可缓存的线程池，它有如下的特点：

- 它是一个可以无限扩大的线程池；
- 阻塞队列采用没有存储空间的`SynchronousQueue`，只有有请求到来，就必须找到一个线程处理它，否则就创建一条新的线程。
- `keepAliveTime`为60S，意味着线程空闲时间超过60S就会被杀死；

```java
ExecutorService cachedThreadPool = Executors.newCachedThreadPool();
for (int i = 0; i < 10; i++) {
    final int index = i;
    Runnable task = () -> {
        System.out.println(index);
        Thread.sleep(2000);
    };
    cachedThreadPool.execute(task);
}
```

`cachedThreadPool`将会无线地处理任务，因此循环执行10个任务时，`cachedThreadPool`能将这10个任务一起执行：

```java
0
1
2
3
4
5
6
//...
```

### 2.3 SingleThreadExecutor

`SingleThreadExecutor`是一个单线程的Executor：

- 它只创建单个工作者线程执行任务
- 阻塞队列采用LinkedBlockingQueue，能确保依照任务在队列中的顺序来串行执行（FIFO、LIFO等）

```java
ExecutorService singleThreadExecutor = Executors.newSingleThreadExecutor();
for (int i = 0; i < 10; i++) {
    final int index = i;
    Runnable task = () -> {
        System.out.println(index);
        Thread.sleep(2000);
    };
    singleThreadExecutor.execute(task);
}
```

由于是单线程串行执行，因此在循环10个任务时，将会一个任务一个任务执行：

```java
0
// 等待2秒
1
// 等待2秒
```

### 2.4 ScheduledThreadPool

`ScheduledThreadPool`是一个固定长度，以延迟或定时的方式来执行任务，类似于Timer。

但是`Timer`线程并不会捕获异常，`Timer`不会恢复线程的执行，将会导致新的任务不能被调度。

```
ScheduledExecutorService scheduledThreadPool = Executors.newScheduledThreadPool(5);

Runnable task = () -> {
    System.out.println("Delay 3 seconds");
};

scheduledThreadPool.schedule(task, 3, TimeUnit.SECONDS);  // 延迟3秒执行该任务
scheduledThreadPool.shutdown();
```

执行效果为：

```java
// 等待3秒
Delay 3 seconds.
```

或者可以设置为延迟3秒执行，然后每隔1秒执行一次：

```
Runnable task = () -> {
    System.out.println("Delay 1 seconds and excute every 3 seconds.");
};

scheduledThreadPool.scheduleAtFixedRate(task, 3, 1, TimeUnit.SECONDS);
```

执行效果为：

```java
// 等待3秒
Delay 1 seconds and excute every 3 seconds.
// 等待1秒
Delay 1 seconds and excute every 3 seconds.
// 等待1秒
Delay 1 seconds and excute every 3 seconds.
```

<br>

> 更多详情可以看[知乎回答](https://www.zhihu.com/question/23212914/answer/245992718)

## 3. 生命周期

`ExecutorService`的声明周期有三种状态：运行、关闭和已终止。`ExecutorService`在初始创建时处于运行状态。它提供两种关闭的方式：

- `shutdown()`：平缓的关闭形式：不再接受新的任务，同时等待已经提交的任务执行完毕。
- `shutdownNow()`：粗暴的关闭形式（类似直接关闭电源）：尝试取消所有运行中的任务，并不再启动队列中尚未开始执行中的任务。