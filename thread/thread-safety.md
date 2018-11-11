## 1. 线程安全性

当多个线程访问某个类时，这个类始终都能表现出正确的行为，那么就称这个类是线程安全的。

那么什么是“正确的行为”：**某个类的行为与其规范完全一致，在良好的规范中通常会定义各种不变形的条件来约束对象的状态，以及定义各种后验条件来描述对象操作的结果**。

简单来说：当多个线程访问同一个对象时，如果**不用考虑这些线程在运行时环境下的调度和交替运行，也不需要进行额外的同步，或者在调用方进行任何其他的协调操作，调用这个对象的行为都可以获取正确的结果**，那这个对象是线程安全的。

例如对于下面的`SafeFactorizer`来说就是线程安全的，因为它是“无状态的”，既不包含任何域，也不包含任何对其他类中域的引用：

```java
@WebServlet(name = "SafeFactorizer", urlPatterns = "/*", loadOnStartup = 1)
public class SafeFactorizer extends HttpServlet {
    
    public int plus(int i) {
        return i + 1;
    }
    
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) {
        int number = Integer.parseInt(req.getParameter("number"));
        int result = plus(number)
        // 返回给客户端计算的结果值
    }
    
}
```

## 2. 原子性

原子性是指操作是不可分的。其表现在于对于共享变量的某些操作，应该是不可分的，必须连续完成。

```java
private long count = 0;

@Override
protected void service(HttpServletRequest req, HttpServletResponse resp) {
    	// 包含了 读取 -> 修改 —> 写入三个操作
		++count;
}
```

**线程安全必须遵循原子性**，但是在上面的`service`方法中，`count++`看似是一个操作，但并非原子的，它并不会作为一个不可分割的操作来执行。

在并发编程中，这种**由于不恰当的执行时序而引发不正确的结果情况叫做竞态条件**。

### 2.1 竞态条件

**当某个计算的正确性取决于多个线程的交替执行时序时，那么就会发生竞态条件**。最常见的竞态条件类型就是“先检查后执行”。

例如在延迟初始化中，能很好地减轻创建对象资源的开销，并能返回单一的实例对象。但是`LazyInitRace`却包含一个竞态条件，在多线程中可能会破坏这个类的正确性：

```java
public class LazyInitRace {

    private ExpensiveObject instance = null;

    public ExpensiveObject getInstance() {
        if (instance == null) {
            return new ExpensiveObject();
        }
        return instance;
    }
}
```

当线程A执行了`getInstance`方法， 检查到`instance`为空，因而创建了新的`ExpensiveObject`对象；此时，线程B也执行了``getInstance``方法，但是此时线程检查出的`instance`是否为空，**取决于不可预测的时序（线程调度方式、A创建对象花费时间）**，如果线程B也检查到`instance`为空。那么在这两次调用`getInstance`方法将会得到不同的结果，而不是单一的实例对象了。

### 2.2 复合操作

我们把这种“先检查后执行”、“读写——修改——写入”（例如递增操作）等操作统称为复合操作：**包含了一组必须以原子方式执行的操作以确保线程安全性**。

## 3. 加锁机制

Java提供了一种内置的锁机制来支持原子性：同步代码块。线程在进入同步代码块之前会自动获得锁，并且在退出同步代码块时自动释放锁（只有一个线程能持有这种锁，即互斥锁）：

```java
// 以Class对象为锁
synchronized (this) {
	// 访问或修改由锁保护的共享状态
}

// 以整个方法为锁
public synchronized service() {
	// 访问或修改由锁保护的共享状态
}
```

这样，我们就可以使用同步方法来实现成功的计数了。但是这种加锁的方式效率十分低，意味着Servlet一次只能处理一个客户端的请求：

```java
private long count = 0;

@Override
protected synchronized void service(HttpServletRequest req, HttpServletResponse resp) {
		++count;
    	// 执行其他耗时的IO操作
}
```

但是，我们**可以通过代码块来缩小同步代码块的作用范围**，就可以很容易地既确保Servlet的并发性，同时又能维护线程的安全性：

```java
private long count = 0;

@Override
protected synchronized void service(HttpServletRequest req, HttpServletResponse resp) {
    synchronized (this) {
        ++count;
    }
    // 执行其他耗时的IO操作
}
```

