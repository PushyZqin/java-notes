## 1. synchronized：

`synchronized`是`java`中同步的关键字，是一种同步锁。可以用来给对象和方法或者某个代码块加锁，当它锁定一个方法或者代码块时，同一时刻最多只能有一个线程可以执行这段代码。

它可以被用来修饰一个代码块、一个实例方法、一个静态方法或者一个类。


## 2. 修饰代码块：

使用`synchronized`来修饰一个代码块，被修饰的代码块被称为同步块（`synchronized block`），作用的范围是大括号`{}`内的代码，作用的对象是调用这个代码块的对象。

当一个线程访问一个对象中被`synchronized`修饰的同步代码块时，其他试图访问该对象的线程将被阻塞，必须等待该线程执行完这个代码块之后，其他线程才能执行该代码块。

```java
public class synchronizedDemo implements Runnable{
    private static int count = 0;
    public void run() {
    	// 非synchronized修饰的代码块：
    	System.out.println("这是非synchronized修饰的代码");
        synchronized (this) {
            for (int i=0;i<3;i++) {
                try {
                    System.out.println(Thread.currentThread().getName() + "：" + count++);
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }
    public static void main(String[] args) {
        synchronizedDemo syncThread = new synchronizedDemo();
        // 创建两个线程，当其中一个线程率先执行syncThread对象的代码块时
        // 另一个线程则需要等它执行完毕，即被阻塞
        Thread T1 = new Thread(syncThread,"SyncThread1");
        Thread T2 = new Thread(syncThread,"SyncThread2");
        T1.start();
        T2.start();
    }
}

// 这是非synchronized修饰的代码
// 这是非synchronized修饰的代码
// SyncThread1：0
// SyncThread1：1
// SyncThread1：2
// SyncThread2：3
// SyncThread2：4
// SyncThread2：5
```

从上面的例子可以看到，当线程`T1`访问`syncThread`的对象的`synchronized`修饰的代码块时，另一个线程`T2`仍然可以访问该对象的非`synchronized`修饰的代码块。

**需要特别注意的是：修饰一个代码块时作用的对象是调用这个代码块的对象**，如果在`main`函数时是这样调用的：

```java
public static void main(String[] args) {
    Thread T1 = new Thread(new synchronizedDemo(),"SyncThread1");
    Thread T2 = new Thread(new synchronizedDemo(),"SyncThread2");
    T1.start();
    T2.start();
}

// SyncThread2：0
// SyncThread1：1
// SyncThread1：2
// SyncThread2：3
// SyncThread2：5
// SyncThread1：4
```

这是因为`synchronized`只锁定对象，当创建了两个`synchronizedDemo`对象时，两个线程执行的分别是两个对象中的`run`方法，对应着`run`方法中被`synchronized`修饰的代码块，因此这两把锁是互不干扰的，两个线程可以同时执行。

## 3. 修饰一个类：

当`synchronized`作用一个类时，作用的对象是这个类的所有对象。也就是说，当`synchronized`作用于一个类时，是给这个类加锁，这个类的所有对象用的是同一把锁。

```java
public class SynchronizedClass implements Runnable{
    private static int count = 0;
    public void run() {
    	// 给该类的所有的对象的该代码块上锁：
        synchronized (SynchronizedClass.class) {
            for (int i=0;i<3;i++) {
                try {
                    System.out.println(Thread.currentThread().getName() + "：" + count++);
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }
    public static void main(String[] args) {
        Thread T1 = new Thread(new SynchronizedClass(),"SyncThread1");
        Thread T2 = new Thread(new SynchronizedClass(),"SyncThread2");
        T1.start();
        T2.start();
    }
}

// SyncThread1：0
// SyncThread1：1
// SyncThread1：2
// SyncThread2：3
// SyncThread2：4
// SyncThread2：5
```

在前一个例子中，我们让两个线程来执行两个不同的对象，两把锁是互不干扰的。但是在这里这个例子中同样创建了两个不同的对象，但是他们的类被`synchronized`所修饰，所以它们使用的是同一把锁。

### 3. 修饰一个方法：

### 3.1 实例方法：

当`synchronized`修饰一个方法时，被修饰的方法称为同步方法，其作用范围是整个方法，作用的对象是调用这个方法的对象。

使用`synchronized`修饰方法，只需要在方法的返回类型前添加关键字`synchronized`：

```java
public class synchronizedDemo implements Runnable{
    private static int count = 0;
    public synchronized void run() {
        for (int i=0; i<3; i++) {
            try {
                System.out.println(Thread.currentThread().getName() + "：" + count++);
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    public static void main(String[] args) {
        Thread T1 = new Thread(new synchronizedDemo(),"SyncThread1");
        Thread T2 = new Thread(new synchronizedDemo(),"SyncThread2");
        T1.start();
        T2.start();
    }
}

// SyncThread1：0
// SyncThread2：1
// SyncThread1：2
// SyncThread2：3
// SyncThread1：4
// SyncThread2：5
```

在通过`synchronized`修饰方法时，**需要注意的是：synchronized关键字不能被继承，例如在父类中定义了某个被synchronized修饰的方法，而在子类中重写了该方法，在子类中该方法是默认是不同步的。**

解决方法是：1.给子类方法也添加上`synchronized`关键字；2.在重写的方法中通过`super.method()`来调用父类中的方法。

### 3.2 静态方法：

当`synchronized`修饰一个静态方法时，其作用的范围是整个静态方法，作用的对象是这个类的所有对象。

这是因为静态方法是属于类而不是属于对象的，与修饰类相似，`synchronized`修饰的静态方法锁定的是这个类的所有对象。

```java
public class synchronizedStaticMethod implements Runnable{
    private static int count = 0;
    public synchronized static void method() {
        for (int i=0;i<3;i++) {
            try {
                System.out.println(Thread.currentThread().getName() + "：" + count++);
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
    public void run() {
        this.method();
    }

    public static void main(String[] args) {
        // 因为静态方法是属于类的，所以即使是实例化两个对象，也是共用一把锁的：
        Thread T1 = new Thread(new synchronizedStaticMethod(),"SyncThread1");
        Thread T2 = new Thread(new synchronizedStaticMethod(),"SyncThread2");
        T1.start();
        T2.start();
    }
}

// SyncThread1：0
// SyncThread1：1
// SyncThread1：2
// SyncThread2：3
// SyncThread2：4
// SyncThread2：5
```

<br><br>

>参考资料：
>
>[Java中Synchronized的用法](http://blog.csdn.net/luoweifu/article/details/46613015)