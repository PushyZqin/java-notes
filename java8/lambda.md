## 1. 最简单的lambda表达式

在`java8`之前，启动一个子线程需要重写`Thread`的`run`方法:

```java
new Thread(new Runnable() {
    @Override
    public void run() {
    	System.out.println("Hello World");
    }
}).start();
```

但是使用了`java8`的`lambda`表达式后，只需要使用一行代码就可以启动一个子线程：

```java
new Thread( () -> System.out.println("Hello World") ).start();
```


## 2. lambda的其他应用

### 2.1 实现对列表的迭代

如果没用使用`lambda`表达式，则通过`for`增强型循环语句进行迭代：

```java
List features = Arrays.asList("Lambdas", "Default Method", "Stream API", "Date and Time API");
for (String feature : features) {
    System.out.println(feature);
}
```

在使用`lambad`之后，只需要一行代码就可以进行迭代打印的操作：

```java
List features = Arrays.asList("Lambdas", "Default Method", "Stream API", "Date and Time API");
features.forEach(n -> System.out.println(n));
// features.forEach(n -> System.out::println);  // 使用java8的新特性“方法引用”
```

### 2.2 使用lambda和Predicate实现过滤操作

`Predicate`函数式接口的主要作用就是提供一个`test()`方法，接受一个参数返回一个布尔类型，`Predicate`在`stream api`中进行一些判断的时候非常常用

在下面的代码中，当`condition.test(name)`传入的`name`值符合`filter`传入的`condition`条件时，将会返回`true`

```java
public class PredicateTest {

    public static void filter(List<String> names, Predicate<String> condition) {
//        for (String name : names) {
//            if (condition.test(name)) {
//                System.out.println(name);
//            }
//        }
		// 使用了stream API和lambda表达式的优雅写法
        // Stream API的过滤方法也接受一个Predicate
        names.stream().filter(name -> (condition.test(name))).forEach((name) -> {
            System.out.println(name);
        });
    }

    public static void main(String[] args) {
        List<String> languages = Arrays.asList("Java", "Scala", "C++", "Haskell", "Lisp");
        filter(languages, (str) -> str.startsWith("J")); // 过滤所有以“J”开头的字符串
    }

}

// java
```

并且，`Predicate` 允许通过逻辑操作符`AND/OR`将两个或更多的 `Predicate` 合成一个:

```java
public static void main(String[] args) {
    Predicate<String> startsWithJ = (n) -> n.startsWith("J");
    Predicate<String> fourletterLong  = (n) -> n.length() == 4;
    languages.stream()
            .filter(startsWithJ.and(fourletterLong))
            .forEach(n -> System.out.println(n));
    }
    
// java
```

### 2.3 与map函数对列表的每个元素应用函数

对列表每个元素进行应用函数可以使用`map`，并且将逻辑以`lambda`的表达式放在`map()`函数中，这样就可以对列表中的每个元素执行转换大写的函数操作：

```java
public static void main(String[] args) {
    List<String> G7 = Arrays.asList("USA", "Japan", "France", "Germany", "Italy", "U.K.","Canada");
    String G7Counties = G7.stream()
            .map(x -> x.toUpperCase())
            .collect(Collectors.joining(","));
    System.out.println(G7Counties);
}

// USA,JAPAN,FRANCE,GERMANY,ITALY,U.K.,CANADA
```