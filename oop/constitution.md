## 1. 创建一个类：

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

## 2. 类的变量：

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

## 3. 类的方法：

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

## 4. 对象：

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