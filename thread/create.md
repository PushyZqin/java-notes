## 1. 创建方法：

Java中提供了两种创建进程的方式：

- 通过实现`Runable`接口，然后在`Thread`类中实例化一个线程对象；

- 继承`Thread`类，重写`run()`方法后创建一个该类的实例。

`Thread`类本身实现了`Runable`接口，**所以**`Runable`**是创建进程实例必须实现的接口，两种方法都必须是实现**`run()`**方法**。

```java
// Thread.java部分源码

class Thread implements Runnable {
    /* Make sure registerNatives is the first thing <clinit> does. */
    private static native void registerNatives();
    ...
```

因为Java只支持单继承，如果作为线程运行的类已经是某一个类的子类了，则不能在继承`Thread`类了，所以大部分情况下还是选择实现`Runable`接口来创建进程实例。

## 2. 实现Runable接口：

我们可以在源码中看到，`Thread`提供一个构造方法接受`Runnable`对象和字符串类型的进程名：

```java
// Thread.java部分源码

public Thread(Runnable target, String name) {
    init(null, target, name, 0);
}
...
```

所以想要创建一个进程，首先声明一个类，实现`Runnable`接口并实现`run()`方法，然后在`Thread`类的构造器创建一个`Thread`对象，通过`start()`方法来启动这个进程：

```java
// RunnableInterface类实现了Runnable接口：
public class RunnableInterface implements Runnable{
    // 实现Runnable的run方法：
    public void run() {
        try {
            System.out.printf("I am %s process\n",Thread.currentThread().getName());
            Thread.sleep(1000); // 线程等待
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Thread " + Thread.currentThread().getName() + " exiting.");
    }
    public static void main(String[] args) {
        // 获得当前（主）进程的进程名：
        System.out.printf("I am %s process \n",Thread.currentThread().getName());
        // 创建Thread对象：
        Thread T1 = new Thread(new RunnableInterface(),"Thread-A");
        T1.start(); // 调用start方法启动进程
    }
}

// I am main process
// I am Thread-A process
// Thread Thread-A exiting.
```

## 3. 继承Thread类：

通过集成`Thread`类创建进程的方式则比较简单粗暴，我们只需要声明一个类继承`Thread`父类，并重写`run()`方法，并实例化该类够通过`start()`调用就创建了一个进程实例：

```java
// ThreadOverWriteRun继承Thread父类：
public class ThreadOverWriteRun extends Thread{
    public ThreadOverWriteRun(String name) {
        // 调用父类Thread的构造函数：
        super(name);
    }
    // 重写Thread的run()方法，作为新进程的路口点：
    public void run() {
        try {
            System.out.printf("I am %s process\n",Thread.currentThread().getName());
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Thread " + Thread.currentThread().getName() + " exiting.");
    }
    public static void main(String[] args) {
        ThreadOverWriteRun t1 = new ThreadOverWriteRun("Thread-B");
        t1.start();
    }
}

// I am Thread-B process
// Thread Thread-B exiting.
```

在JAVA8新增的`lambda`匿名函数后，我们可以通过比较优雅的方式来创建一个进程实例：

```java
public class lambdaMethod {
    public static void main(String[] args) {
        new Thread(() -> subProcess(),"Thread-C").start();
    }
    public static void subProcess() {
        System.out.printf("I am %s process\n",Thread.currentThread().getName());
    }
}
```

<br><br>

>参考资料:
>
>[编程思想之多线程与多进程(3)——Java中的多线程](http://blog.csdn.net/luoweifu/article/details/46673975)
