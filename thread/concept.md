# 1. 线程的生命周期

-  `NEW`：当`new`创建一个`Thread`对象时，那么该线程状态处于`NEW`状态。此时它需要通过`start()`方法来进入`RUNNABLE`状态。
-  `RUNNABLE`：即可执行状态，线程在进入`RUNNABLE`状态之前必须调用`start()`方法，那样才真正地在JVM线程中创建了一个线程，并等待CPU的调度。
-  `RUNNING`：一旦CPU通过轮询或者其他方式从任务可执行队列中选中了线程，那么该线程才能真正执行自己的逻辑代码。实际上`RUNNING`状态也是`RUNNABLE`。

- `BLOCKED`：即阻塞状态，`RUNNING`状态的线程通过`sleep`、`wait`方法或者通过获取某个锁的资源而加入到该锁的阻塞队列中等操作，变成了`BLOCKED`状态。
- `TERMINATED`：即一个线程的终止状态，意味着该线程的整个生命周期都结束了。无法再切换到其他的线程状态。

# 2. Runnable的设计模式

`Runnable`的引入采用了策略模式的设计模式，**将线程的控制本身和业务逻辑的运行分离开来**，达到职责分明，功能单一的原则。

也就是说它们的分工为：

- Thread **负责线程本身相关的职责和控制**；
- Runnable **负责逻辑执行单元的部分**。

# 3. 与JVM联系

与线程创建、运行、销毁等关系比较大的就是虚拟机内存了，而且栈内存划分的大小将直接决定一个JVM进程中可以创建多少个线程——当堆内存不变时，如果栈内存越大，可以创建的线程数量就越少。

# 4. 守护线程

## 4.1 与普通线程区别

在下面代码中，创建一个子线程无线循环，当主线程在休眠2秒之后将结束生命周期，但是JVM线程却始终不退出：

```java
public class DaemonThread {
    public static void main(String[] args) throws InterruptedException {

        Thread thread = new Thread(() -> {
            while (true) {
                try {
                    Thread.sleep(1);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });

		// thread.setDaemon(true); 将线程设置为守护线程

        thread.start();
        Thread.sleep(2000);
        System.out.println("Main thread finished lifecycle.");
    }
}
```

但是如果将子线程设置为守护线程（通过`thread.setDaemon(true)`方法），那么在主线程完成生命周期之后，JVM也将结束进程。

## 4.2 作用

守护线程具有自动结束生命周期的特性，而非守护线程（普通线程）则不具备该特点（也就导致了JVM在`main`线程结束之后，将不会退出）。

守护线程进程用作与执行一些后台任务，也被称之为后台线程。当你希望关闭某些线程时，或者退出JVM进程时，一些线程能自动关闭，就可以用守护线程来完成。