## 1. 定义：

泛型的本质为参数化类型，即**将类型有原来的具体类型参数化**，在不创建新的类型的情况下，通过泛型指定的不同类型来控制形参具体限制的类型。

实际使用来说，操作的数据类型被指定为一个参数，这种参数类型可以用在类、接口和方法当中，分别对应着泛型类、泛型接口、泛型方法。

另外，泛型还有一个特性：**只在编译阶段有效，也就是说泛型信息是不会进入到运行时的阶段的**。

```java
public class equalsClass {
    public static void main(String[] args) {
        List<String> strList = new ArrayList<String>();
        List<Integer> itrList = new ArrayList<Integer>();

        if (strList.getClass().equals(itrList.getClass())) {
            System.out.print("泛型类型相同");
        }
    }
}

// 输出：泛型类型相同
```

通过上面的说明可以总结成：**泛型类型在逻辑上看以看成是多个不同的类型，实际上都是相同的基本类型**

## 2. 泛型类：

### 2.1 声明泛型类：

泛型类的声明和非泛型类的声明类似，除了需要在类名后面添加类型参数声明部分。

例如，我们定义一个泛型类`GenericClass`，这里的`T`为任意标识符，当实例化这个泛型类时，需指定`T`的具体类型，类中所定义的变量类型，方法返回类型都是由外部的`T`决定：

```java
public class GenericClass<T> {
	// foo变量类型为T，由外部指定：
    private T foo;
    public GenericClass(T foo) {
        this.foo = foo;
    }
    public T get() {
        return foo;
    }
}
```

### 2.2 实例化泛型类：

在实例化这个泛型类时，需要指定具体的类类型，而不是简单的类型，**并且传入的实参类型需要与泛型的类型参数类型相同**，这里为`Integer`和`String`：

```java
// 指定的泛型类型为Integer：
GenericClass<Integer> gri = new GenericClass<Integer>(123456);
// 指定的泛型类型为String：
GenericClass<String> grs = new GenericClass<String>("Hello");

gri.get();  // 返回123456
grs.get(); // 返回Hello
```

但是，如果实例化泛型类时并没有传入泛型类型实参的话，则**在泛型类中使用的泛型方法和成员变量的定义的类型可以为任何类型**：

```java
GenericClass noType = new GenericClass(1.1);
noType.get() // 返回1.1
```

## 3. 泛型方法：

首先需要知道的一点是：**泛型类中定义的成员方法并不是泛型方法**，因为该方法的返回值是在声明泛型类中声明过的泛型了，例如下面的`func()`方法：

```java
public class Generic<T> {
	private T foo;
    public Generic(T foo) {
    	this.foo = foo;
    }
    public T getFoo() {
        return foo;
    }
}
```

定义一个泛型方法，**必须要在返回类型之前加上**`<E>`（与泛型类一致，`E`为自定义字符），可以理解为声明此方法为泛型方法：

```java
public class GenericMethod {
    // <E>为声明一个类型参数声明部分，在返回类型之前:
    public <E> void printArray(E[] inputArray){
        for (E element:inputArray) {
            System.out.printf("%s ",element);
        }
    }
    public static void main(String[] args) {
        Integer[] intArray = {1,2,3,4,5};
        Character[] charArray = {'H','E','L','L','O'};
        printArray(intArray);  // 传递一个整形数组
        printArray(charArray); // 传递一个字符型数组
     }
}
```

另外**如果一个静态方法需要泛型数据的话，必须将静态方法也定义成泛型方法**，即使静态方法要使用泛型类中已经声明过的泛型也不可以。

```java
public class StaticGenerator<T> {
    public static <T> void show(T t){
		// other code..
    }
}
```

## 4. 类型通配符：

### 4.1 所有具体类型：

`List<?>`代表了所有List（具体类型实参）的父类：

```java
public class GenericTest {
    public static void main(String[] args) {
        List<String> name = new ArrayList<String>();
        List<Integer> age = new ArrayList<Integer>();
        List<Number> number = new ArrayList<Number>();

		name.add("icon");
        age.add(18);
        number.add(314);

		getData(name);
        getData(age);
        getData(number);
   }

	public static void getData(List<?> data) {
      System.out.println("data :" + data.get(0));
   }
}
```

### 4.2 限定泛型上限：

`List<? extends Number>`限定了泛型的上限，代表的类型是`Number`的子类：

```java
public static void getUperNumber(List<? extends Number> data) {
    System.out.println("data: " + data.get(0));
}
```

### 4.3 限定泛型下限：

`List<? super Integer>`限定了泛型的下限，代表的类型是`Interger`的父类：

```java
public static void getUperInteger(List<? super Integer> data) {
    System.out.println("data: " + data.get(0));
}
```

<br><br>

>参考资料：
>
>[ java 泛型详解-绝对是对泛型方法讲解最详细的，没有之一](http://blog.csdn.net/s10461/article/details/53941091)