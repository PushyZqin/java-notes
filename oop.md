## 1.类的构成：

### 1.1 创建一个类：

#### 创建父类：

类可以看成是创建Java对象的模板，每一个实例都是根据这个模板创建的对象:

新建一个类：`Animal`就是一个类，并且这个类和文件名一致：`Animal.java`

```java
public class Animal(){
    ...
}
```

#### 创建子类：

创建子类可以通过继承，使用 `extends` 和 `implements`（变相的使java具有多继承的特性）这两个关键字来实现继承：

```java
public class Animal {
	...
}

class Mouse extends Animal {
	...
}
```

### 1.2 :类的变量：

#### 局部变量：

在方法（包括构造方法）内定义的变量，作用域只在该函数的代码块中，其声明和初始化都在方法中，方法执行结束后将销毁。**因为局部变量没有默认值，所以当局部变量被声明后，必须初始化才可以使用**：

```java
public class  ClassName{
    public void printNumber（）{
        int a = 0;  // 声明并初始化
        a = a + 7;
    }
    // other code...
}
```

#### 成员（实例）变量：

定义在类中，方法体之外。变量在创建对象时初始化，它可以被类的方法所访问，并在对象被销毁的时候销毁。访问修饰符可以修饰成员变量，并且成员变量具有默认值为（如数值型为`0`，布尔型为`false`）：

```java
public class  ClassName{
    public int a;  // 定义一个对子类可见的成员变量
    public void printNumber（）{
        // other code...
    }
}
```

另外，实例变量是在整个内部都是可访问的，而不管实例变量声明在类的那个位置，即使是在类的尾部都是可以访问的：


```java
public class Employee{
    public Employee (String empName){
        name = empName;
    }
    public static void main(String args[]){
		// other code...
	}
    public String name;  // 在尾部声明一个实例变量
}
```

#### 类（静态）变量：

类变量也声明在类中，方法体之外，在程序开始时创建，在程序结束时销毁。但是必须声明为`static`类型，其实质上就是一个全局变量。类变量同样可以被访问修饰符修饰、同样具有默认值。

无论一个类实例化多少个对象，它的静态变量只有一份拷贝，**局部变量不能被声明为static变量**。

```java
public class ClassName{
    private static int a;  // 定义一个静态的私有变量
    public void printNumber（）{
        // other code...
    }
}
```

### 1.3 方法：

#### 构造方法：

如果没有指定，编译器将会自动提供一个默认的构造方法，并且一个类可以有多个构造方法。在创建对象的时候，至少要调用一个构造方法，**并且构造方法的名称必须与类同名**。

```java
public class Hello{
    public Hello() {
    	// 这个构造器没有参数
    }
    public Hello(String name) {
    	// 这个构造器仅有一个参数：name
    }
    public static void main(String[] args) {
    	// 调用第一个构造器：
    	Hello h = new Hello();
        // 传入name参数将调用第二个构造器:
        Hello hn = new Hello("Pushy");
    }
}
```

#### 静态方法：

静态方法被`static`修饰符修饰，**在静态方法中不能使用类的非静态变量**：

```java
public class StaticMethod {
    public String name = "Pushy";  // 定义个成员变量
    public static void method() {
        string = "Harry";  // 报错，无法使用成员变量
    }
}

```

#### 一般方法：

```java
public class Method {
    int puppyAge;
    public void setAge( int age ) {
       puppyAge = age;
    }
}
```

### 1.4 对象：

#### 创建对象：

创建一个对象的步骤是：声明——实例化（new）——初始化（将调用构造方法）:

```java
public class Puppy{
    public static void staticMethod() {
        System.out.println("static method");
    }
    public Puppy(String name){
      //这个构造器仅有一个参数：name
        System.out.println("The dog's name is  : " + name ); 
    }
    public static void main(String []args){
    	// 不用实例化对象可以直接调用静态方法：
		staticMethod()
        // 下面的语句将创建一个Puppy对象
        Puppy myPuppy = new Puppy( "tommy" );
    }
}
```

#### 访问对象的变量和方法：

```java
//实例化对象
Puppy myPuppy = new Puppy( "tommy" );
//访问成员变量：
Puppy.puppyAge ;
//调用一般方法：
Puppy.getAge();
```

## 2. 修饰符：

### 2.1 访问权限修饰符：

访问权限修饰符可以用来保护对类、变量、方法和构造方法的访问：

以下为访问权限修饰符变量的访问权限控制：

| 修饰符       | 当前类  | 同一包内 | 子孙类  | 其他包  |
| --------- | ---- | ---- | ---- | ---- |
| default   | Y    | Y    | N    | N    |
| public    | Y    | Y    | Y    | Y    |
| protected | Y    | Y    | Y    | N    |
| private   | Y    | N    | N    | N    |

```java
default // (即缺省，什么也不写）: 在同一包内可见，不使用任何修饰符。使用对象：类、接口、变量、方法
private // (最高级): 在同一类内可见。使用对象：变量、方法。 注意：不能修饰类（外部类）
public : // 对所有类可见。使用对象：类、接口、变量、方法
protected : // 对同一包内的类和所有子类可见。使用对象：变量、方法。 注意：不能修饰类（外部类）
```

### 2.2 非访问权限修饰符：

非访问权限的修饰符包括如下的几种：

#### static：

`static`可以用来修饰类（静态）变量和类（静态）方法:

```java
public class StaticModifier {
	private static int a = 0;
    private static void changeValue(){
    	a = 1
    }
}
```

#### final：

`final`修饰符可以用来修饰类、方法、变量：

- 修饰的类不能被继承，没有类能够继承`final`类的任何特性：

```java
final class finalClass {
    public String name = "Pushy";
}

// 报错：无法从final Class进行继承
class SubFinalClass extends finalClass {
    public static void main(String[] args){
        SubFinalClass s = new SubFinalClass();
        System.out.println(s.name);
    }
}
```

- 修饰的方法可以被子类继承，但是**不能被子类修改**：

```java
public class finalMethod {
    String name = "Pushy";
    public final void changeName() {
        name = "Harry";
    }
}

class subFinalMethod extends finalMethod {
    // 报错，subFinalDemo中的changeName()无法覆盖finalDemo中的changeName()
    // 因为被覆盖的方法为final修饰
    public void changeName() {
        // 重写changeName方法：
        name = "Via";
    }
    public static void main(String[] args) {
        subFinalDemo s = new subFinalDemo();
        s.changeName();
    }
}
```

- 修饰的变量**只能被显式地初始化一次**，`final`对象的引用不能改变，但是里面的值可以改变：

```java
public class finalDemo {
    final int value = 10;
    public void changeValue(){
        value = 12;  // 报错，final修饰的常量只能初始化一次
    }
    public static void main(String[] args) {
        finalDemo f = new finalDemo();
        f.changeValue();
    }
}
```

#### abstract：

`abstract`用来创建抽象类和抽象方法，定义抽象类和抽象方法的目的是：**为了把相同的但却不确定的东西提取出来，为了以后的重用。定义成抽象类的目的，就是为了在子类中实现抽象类**。

因此，当子类`Cat`继承了抽象类`Animal`后，必须去实现`cry()`方法。也就是说**当继承的父类是抽象类时，需要将该父类的所有抽象方法全部实现**：


```java
abstract class Animal {
    String name;
    int age;
    // 定义一个抽象方法，来解决父类方法的不确定性
    // 抽象方法在父类不能实现（实例化），所以没有函数体
    public abstract void cry();
}

class Cat extends Animal {
	// 在子类中实现父类的cry方法：
    public void cry() {
        System.out.println("猫叫：");
    }
    public static void main(String[] args) {
        Cat c = new Cat();
        c.cry();  // 调用该方法
    }

}
```


## 3. 多态性：

### 3.1 Override（重写）：

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

### 3.2 Overload（重载）：

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

### 3.3 重写和重载区别：

![](http://pan.pushy.site/_uploads/files/olAndor.jpg)

重写是子类覆盖与父类参数个数、类型，返回值相同的函数的行为。重写的作用是**子类需要做出当输入的数据一样，但有别于父类处理的响应**。

重载是父类中存在多个方法名相同，但参数列表不同的定义，是多态性的具体表现。重载的作用是**能够根据输入的数据不同，做出不同的处理**。


### 3.4 多态：

#### 多态的实现：

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


#### 多态性与数据成员：

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




<br><br><br><br><br><br><br><br>