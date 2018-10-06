## 1. 接口定义

接口是抽象方法的集合，通过`interface`来声明，一个类可以通过继承接口的方式，来继承接口中所有的抽象方法。需要明白的是：**类是描述对象的属性和方法，接口包含类要实现的方法集合，所以接口≠类**。

![](http://www.yiibai.com/uploads/images/201703/0703/957110359_32640.png)

根据上图说明：编译时，接口中的方法会隐式指定为`public abstract`，变量则会被隐式地指定为：`public static final`。

例如我们可以这样声明一个接口：

```java
public interface A {
	void run(); // 不允许有方法体
}
```

如果某个类继承（类继承接口的关键字为`implements`）了接口`A`，则**需要实现该接口的所有抽象方法**：

```java
public class interfaceTest implements A {
	public void run() {
        // do something...
    }
    public static void main(String[] args) {
        interfaceTest obj = new interfaceTest();
        obj.run();
    }
}
```

## 2. 接口方法

## 2.1 默认方法

JAVA`8`中，可以为接口方法提供一个默认实现，用`default`修饰符来标记这样一个方法。

```java
public interface MyCollection {
    int size();
    
    default boolean isEmpty() {
        return size() == 0;
    }
}
```

这样实现`MyCollection`接口就不用操心`isEmpty`方法了：

```java
public class MyList implements MyCollection {
    private int size;

	@Override
    public int size() {
        return size;
    }
}
```

使用`MyList`则可以使用`isEmpty()`方法：

```java
MyCollection list = new MyList();
list.isEmpty()
```

## 2.2 静态方法

我们还可以声明一个静态方法，该方法也可以通过类继承接口后直接调用：

```java
public interface C {
    static int plus(int x) {
        return x + x;
    }
}
```


## 3. 多继承接口：

如果一个类实现了多个接口：

```java
class Inheritance implements interfaceA, interfaceB {
	// other code...
}
```

或者一个接口扩展了多个接口，则被称为多重继承：

```java
public interface interfaceC extends interfaceA, interfaceB {
	// other code...
}
```

虽然在类中多继承是不合法，但是通过接口可以实现变相的多重继承：

```java
interface Printable {
    void print();
}

interface Showable {
    void show();
}

class InheritanceTest implements Printable, Showable {
    public void print() {
        System.out.println("Hello");
    }

    public void show() {
        System.out.println("Welcome");
    }

    public static void main(String args[]) {
        InheritanceTest7 obj = new InheritanceTest();
        obj.print();
        obj.show();
    }
}
```

## 4. 抽象类和接口区别：


### 4.1 异同点：

首先，抽象类是实现部分抽象（0-100%），而接口则是完全抽象（100%）。它们具有一些共同点，例如都用于实现抽象、可以声明抽象方法、都不能被实例化。

另外，它们还具有如下的不同点：

| 单位\类别 | 抽象类                    | 接口                         |
| ----- | ---------------------- | -------------------------- |
| 方法    | 可以有抽象和非抽象方法            | 仅有抽象方法，从java8开始，可有有默认和静态方法 |
| 多继承   | 不支持                    | 支持                         |
| 变量    | 可以有final、非final，静态与非静态 | 仅有final和静态                 |
| 关系    | 抽象类可以提供接口的实现           | 接口不能提供抽象类的实现               |
| 声明    | abstract关键字            | interface关键字               |

### 4.2 取舍：

那么，我们什么时候使用抽象类，什么时候使用接口呢？

- 如果你拥有一些方法并且想让它们中的一些有默认实现，那么使用抽象类吧。

- 如果你想实现多重继承，那么你必须使用接口。由于Java不支持多继承，子类不能够继承多个类，但可以实现多个接口。因此你就可以使用接口来解决它。

- 如果基本功能在不断改变，那么就需要使用抽象类。如果不断改变基本功能并且使用接口，那么就需要改变所有实现了该接口的类。


———— 摘自[什么时候使用抽象类和接口](http://www.runoob.com/java/java-interfaces.html)