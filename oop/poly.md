## 1. Override（重写）：

重写是发生在子类当中的，子类根据需要重新定义父类中的方法称为重写。

方法重写（覆盖）的规则有：

- 覆盖的方法必须与父类中的名称相同，必须**具有与重写的方法相同的参数和返回类型**；

- 不能覆盖被`static`、`final`修饰的方法和构造方法；

- 重写的方法不能抛出新的检查异常或者比重写方法申明更加宽泛的异常，例如父类申明一个检查异常`IOException`，则子类不能抛出`Exception`异常。


```java
class Animal {
    public void move() {
        System.out.println("动物是可以移动的");
    }
}

class Bird extends Animal3 {
	// 重写move()方法：
    public void move() {
        System.out.println("鸟可以飞翔");
    }
    public void bark() {
        System.out.println("鸟可以喳喳叫");
    }
}

public class OverrideDemo {
    public static void main(String[] args) {
        Animal a = new Animal();
        Animal b = new Bird();
        a.move();
        b.move();
//        b.bark();  b应用类型Animal没有bark方法
    }
}
```

## 2. Overload（重载）：

重载是发生在一个类中（不存在继承关系），方法名字相同，而参数列表不同，返回类型相同或者不相同，例如构造器的重载。

在java中，重载的规则有：

- 被重载的方法**必须改变参数列表（参数个数或个数或顺序不一样）**；

- 被重载的方法可以改变返回类型和修饰符；

- 被重载的方法**可以声明新的或者更广的检查异常**。


例如不同参数的个数重载方式：

```java
public class OverloadingDemo {
    static int add(int a, int b) {
        return a + b;
    }
    static int add(int a, int b, int c) {
        return a + b + c;
    }
    public static void main(String[] args) {
        System.out.println(OverloadingDemo.add(11,11));
        System.out.println(OverloadingDemo.add(11,11,11));
    }
}
```

不同类型数据的重载方式：

```java
class OverloadingDemo2 {
    static int add(int a, int b) {
        return a + b;
    }
    static double add(double a, double b) {
        return a + b;
    }
    public static void main(String[] args) {
        System.out.println(OverloadingDemo2.add(11,11));
        System.out.println(OverloadingDemo2.add(12.3,12.6));
    }
}
```

## 3. 重写和重载区别：

![](https://static.pushy.site/files/olAndor.jpg)

重写是子类覆盖与父类参数个数、类型，返回值相同的函数的行为。重写的作用是**子类需要做出当输入的数据一样，但有别于父类处理的响应**。

重载是父类中存在多个方法名相同，但参数列表不同的定义，是多态性的具体表现。重载的作用是**能够根据输入的数据不同，做出不同的处理**。


## 4. 多态：

### 4.1 多态的实现：

首先，多态必须存在**继承、重写、父类引用指向子类对象**这三个条件，例如：

```java
public class Animal {
	void run {
    	...
    }
}
// 继承Aniaml类：
class Dog extends Animal {
	void run {
	    // 重写run方法：
    }
    void bark {
    	System.out.println("汪汪叫");
    }
    public static void main(String() args) {
    	Animal d = new Dog(); // 父类引用指向子类对象，称为向上转换
        d.run();  // 将调用子类的run方法
        //d.bark(); // 无法调用bark方法
    }
}
```

当使用多态的方式调用方法时，首先会检查父类是否具有该方法，如果没有则编译错误（**说明了该引用只能调用父类中定义的方法**）；如果有，再去调用子类同名的方法（重写的方法）。


### 4.2 多态性与数据成员：

在使用多态时，需要注意的是：**多态性不能由数据成员来实现**。

例如在下面的代码中，`Bike`引用指向`Splender`对象，在访问`speedLimit`成员变量时返回的是父类的成员变量；而通过调用`speed()`方法则调用的是`Splender`类中方法和其成员变量。

```java
public class Bike {
    int speedLimit = 90;
    void speed() {
        System.out.println(speedLimit);
    }
}

class Splender extends Bike {
    int speedLimit = 150;
    void speed() {
        System.out.println(speedLimit);
    }
    public static void main(String args[]) {
        Bike b = new Splender();
        b.run();
        // 运行时多态性不能由数据成员实现，只能通过方法来实现:
        System.out.println(b.speedLimit);
        b.speed();
    }
}
```

运行结果为：

```
90
150
```