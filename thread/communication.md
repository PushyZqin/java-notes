## 1. wait/notify 机制：

该机制主要是通过`wait()`、`notify()`、`notifyAll`三个方法来保证的，它们都是`Object`类的最终方法(被`final`修饰的方法)，意味着所有的`Java`类都拥有该方法并且无法被重写。

虽然每个类都存在这些方法，但是必须在`synchronized`修饰的作用范围，并且是同一个同步问题中搭配其中的方法才有意义。

### 1.1 wait()：

`wait()`方法是让当前的线程释放对象锁，并进入等待（阻塞）状态，等待其他线程通过`notify()/notifAll()`方法激活。

**需要注意的是必须要在synchronized修饰的代码块或者方法中调用该方法**：

```java
public static void Producer(){
    synchronized (this) {
        lock.wait();
        // other code
    }
}
```

除了可以通过`notify()`方法来唤醒当前等待的线程，我们也可以使用`interrupt()`方法来中断调用了`wait()`方法而进入等待队列的线程：

```java
public class interruptDemo {
    public static void threadRun (Object lock) {
        synchronized (lock) {
            try {
                lock.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
                System.out.println("Have some error...");
            }
        }
    }
    public static void main(String[] args) throws InterruptedException{
        Object lock = new Object();
        Thread thread = new Thread(() -> threadRun(lock),"Thread-B");
        thread.start();
        Thread.sleep(2000);
        // 等待两秒后将抛出InterruptedException错误：
        thread.interrupt();
    }
}
```

### 1.2 notify()/notifyAll()：

当执行这俩方法时，**会唤醒一个处于等待该对象锁的线程**，然后继续往下执行（说明执行后并不理解释放锁），直到执行完退出`synchronized`修饰的范围后再释放锁。

```java
public class notifyDemo {
    public static void waitMethod(Object lock) {
        synchronized (lock) {
            try {
                lock.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
    public static void notifyMethod(Object lock) {
        synchronized (lock) {
            try {
                lock.notify();
                Thread.sleep(1000);
                // 因此不要在notify/notifyAll方法后边写耗时的代码：
                System.out.println("还将执行该代码");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
    public static void main(String[] args) {
        Object lock = new Object();
        new Thread(() -> waitMethod(lock),"Thread-A").start();
        new Thread(() -> notifyMethod(lock),"Thread-B").start();
    }
}

// 还将执行该代码
```

两个方法的区别在于：`notify`只能唤醒一个正在处于`wait`之后阻塞状态的线程；而`notifyAll`则是唤醒所有处于`wait`状态的线程，使其重新进入锁的争夺队列中。


### 1.3 生产者/消费者模型：

生产者添加队列之后，通过`notify`通知消费者进行消费，自身第二次`while`循环后进入`wait`状态；处于`wait()`状态消费者受到通信后，开始消费，消费完成之后通过`notify`通知生产者进行生产：

可以参考代码：[ConsumerProducer.java](https://github.com/PushyZqin/java-demo/blob/master/Thread/src/communication/ConsumerProducer.java)