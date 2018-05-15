## 1. Stream

>Stream 作为 Java 8 的一大亮点，它与 java.io 包里的 InputStream 和 OutputStream 是完全不同的概念。它也不同于 StAX 对 XML 解析的 Stream，也不是 Amazon Kinesis 对大数据实时处理的 Stream。Java 8 中的 Stream 是对集合（Collection）对象功能的增强，它专注于对集合对象进行各种非常便利、高效的聚合操作（aggregate operation），或者大批量数据操作 (bulk data operation)。Stream API 借助于同样新出现的 Lambda 表达式，极大的提高编程效率和程序可读性。同时它提供串行和并行两种模式进行汇聚操作，并发模式能够充分利用多核处理器的优势，使用 fork/join 并行方式来拆分任务和加速处理过程。通常编写并行代码很难而且容易出错, 但使用 Stream API 无需编写一行多线程的代码，就可以很方便地写出高性能的并发程序。所以说，Java 8 中首次出现的 java.util.stream 是一个函数式语言+多核时代综合影响的产物
>
>[Java 8 中的 Streams API 详解
](https://www.ibm.com/developerworks/cn/java/j-lo-java8streamapi/#icomments)

### 2.1 定义

Stream是使用的是有种类似SQL语句从数据库查询数据的直观方式来对Java集合运算和表达的高阶抽象。

这种风格将要处理的元素集合看做一种流，流在管道中传输，并且可以在管道中的节点上进行筛选、排序、聚合等操作。元素流在管道中经过中间操作（`intermediate operation`）的处理，最后由最终操作(`terminal operation`)得到前面处理的结果

```
+--------------------+       +------+   +------+   +---+   +-------+
| stream of elements +-----> |filter+-> |sorted+-> |map+-> |collect|
+--------------------+       +------+   +------+   +---+   +-------+
```

流的操作分为`intermediate`和`terminal`。一个流可以后面跟随零个或多个的`intermediate`操作，目的是打开流，做出数据映射(`map`)/过滤(`filter`)的操作，然后返回一个新的流，交给下一个操作进行，这类操作都是惰性化的(`lazy`，即调用这类方法，并没有真正开始流的遍历)；但是，一个流只能有一个`terminal`操作，当这个操作执行后，流就并使用完了将无法被操作，只有当`terminal`操作执行，才会真正开始流的遍历，并且会生成一个结果（或`side effect`）。

### 2.2 基本使用

#### 流的构造

除了从以下的方法构造流，还有很多种方式来生成流，例如`Random.ints()`、`BitSet.stream()`等。

```java
// 从单个值从构造
Stream stream = Stream.of("a", "b", "c");
// 从数组构造
String [] strArray = new String[] {"a", "b", "c"};
stream = Stream.of(strArray);
stream = Arrays.stream(strArray);
// 从集合构造
List<String> list = Arrays.asList(strArray);
stream = list.stream();
```

#### 流的转换

流的转换主要是通过`collect`聚合操作来实现的

```java
// 转换成数组
String[] strArray1 = stream.toArray(String[]::new);
// 转换成集合元素
List<String> list1 = stream.collect(Collectors.toList());
List<String> list2 = stream.collect(Collectors.toCollection(ArrayList::new));
Set set1 = stream.collect(Collectors.toSet());
Stack stack1 = stream.collect(Collectors.toCollection(Stack::new));
// 转换为字符串，并可以通过joining()方法连接流中每个元素
String str = stream.collect(Collectors.joining()).toString();
```

### 2.3 基本方法

流的基本具有两种基本的方法，一种为`Intermediate`，例如`map()`、`filter()`等。一种为`Terminal`，例如`forEach()`、`toArray()`等。

#### map

`map()`方法的作用就是把`input stream`的每一个元素，通过操作后映射成`out stream`的另外一个元素。

```java
List<String> output = wordList.stream().
map(a -> a.toUpperCase()). // 进行映射的操作
collect(Collectors.toList());  // 转化成List集合
```

#### filter

`filter` 方法用于通过设置的条件过滤出元素

```java
List<String>strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl");
// 筛选出不为空的字符串后，得到筛选后数组的数量
int count = strings.stream()
				.filter(string -> string.isEmpty())
                .count();

```

#### forEach

`forEach`方法接受一个`Lambda`表达式，然后再Stream的每一个元素上执行该表达式：

```java
Random random = new Random();
random.ints()  // 从Random对象中生成流
	.limit(10)  // limit 返回 Stream 的前面 n 个元素
    .forEach(System.out::println);
```

需要注意的是，`forEach`属于`Terminal`操作，也就是说执行完`forEach()`方法后，流就无法被继续使用了，如果需要在进行相同的操作后，仍然继续是该流，可以通过`peek()`方法来实现：

```java
Stream.of("one", "two", "three", "four")
 .filter(e -> e.length() > 3)
 .peek(e -> System.out.println("Filtered value: " + e))
 .map(String::toUpperCase)
 .peek(e -> System.out.println("Mapped value: " + e))
 .collect(Collectors.toList());
```

#### sorted

对 `Stream` 的排序通过 sorted 进行，它比数组的排序更强之处在于你可以首先对 `Stream` 进行各类 `map`、`filter`、`limit`、`skip` 甚至 `distinct` 来减少元素数量后，再排序，这能帮助程序明显缩短执行时间

```java
List<User> userList = Arrays.asList(new User("Pushy",19),new User("Jacking",22));

List<String> sortedUserNameList =  userList.stream()
		// 进行排序操作
        .sorted((u1,u2) -> u1.getUsername().compareTo(u2.getUsername()))
        .map(User::getUsername) // 取值
        .collect(Collectors.toList());
System.out.println(sortedUserNameList);

// [Jacking, Pushy]
```

<br>

>参考资料
>
>[Java 8 中的 Streams API 详解](https://www.ibm.com/developerworks/cn/java/j-lo-java8streamapi/#icomments)
>[Java 8 Stream](http://www.runoob.com/java/java8-streams.html)

## 2. Optional

### 2.1 定义和基本方法

`Optional` 类是一个可以为`null`的容器对象。如果值存在则`isPresent()`方法会返回`true`，调用`get()`方法会返回该对象，该类能很好的解决空指针异常。

```java
public static void main(String[] args) {
    Integer value = 1;
    // Integer value = null;
    Optional<Integer> a = Optional.ofNullable(null);
    System.out.println("a是否存在 " + a.isPresent());
    // 如果a存在，则返回a，不存在则返回0
    System.out.println(a.orElse(new Integer(0)));
    // 如果a存在，则执行map中函数操作（自增1），不存在则返回0
    System.out.println(a.map(f -> f += 1).orElse(0));
}

// a是否存在 true
// 1
// 2

// a是否存在 false
// 0
// 0
```

`Optional`提供了一些基本的方法，例如：

- 存在则返回，无则提供指定的值：

```java
return object.orElse(null)
```

- 存在则返回，无则通过函数来产生：

```java
return object.orElseGet( () -> fetchRandomObject() )
```

- 存在则提供的映射方法（`lambda`表达式），无则返回一个空集合（`Optional.empty`）：

```java
return object.
		.map(obj -> obj.getPropertiy())
        .orElse(null)
```


### 2.2 应用

例如存在一个通过用户名查询用户名密码的`service`方法，并且返回查询用户的密码。需要调用`fetchUserByUserName`的`dao`方法，但是不能保证查询出的user对象是否为`null`，所以可以使用`Optional`类来避免出现空指针异常。

```java
@Overide
public String fetchPasswordByNameSerivce(String name) {
	User user = userMapper.fetchUserByUserName(name);
    return Optional.ofNullable(user)  // 允许从null中创建一个Optional对象
    	.map(user -> user.getUserPassword()) // 当user存在时，返回user对象的password
        .orElse("None") // 当user不存在时，在返回None
}
```

并且`map()`是无限极联的，例如假设一个用户只关联一个角色，那么在一个通过用户名查询到该用户的角色的角色名`service`方法就可以这样实现：

```java
@Overide
public String fetchRoleNameByNameSerivce(String name) {
	User user = userMapper.fetchUserByUserName(name);
    return Optional.ofNullable(user)  // 允许从null中创建一个Optional对象
    	.map(user -> user.getRole()) // 如果存在得到user关联的role对象
        .map(role -> role.getRoleName()) // 如果存在得到role的角色名
        .map(roleName -> roleName.toUpperCase()) // 将角色名转换成大写的形式
        .orElse("None")
}
```

<br>

>参考资料
>[【Java】jdk8 Optional 的正确姿势](https://blog.csdn.net/hj7jay/article/details/52459334)
