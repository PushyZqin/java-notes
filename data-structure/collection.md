## 1. 集合框架结构

Java中的容器包括`Collection`和`Map`两种，`Collection`包括了`List`、`Set`继承自它的接口，`Map`包括了`Map`继承自它的接口。宏观来说，`Collection`和`Map`为容器的接口，`List`/`Set`/`Map`为继承它们的子接口，而`Collections`是一个工具类，提供了对集合进行排序，遍历等多种算法的实现。

在`List`、`Set`、`Map`存取元素时，有各自的特点和适用情况：

- `List` 以特定索引来存取元素，可以有重复的元素；

- `Set` 不能存放重复的元素；

- `Map` 为键值对映射容器，用来保存键值对映射。

而`List`等继承自`Collection`的子接口还包含了实现该接口的具体类，例如`List`的子类有`ArrayList`/`LinkedList`，`Set`的子类有`HashSet`/`TreeSet`等等。

需要注意的一点是，`Map`**的子接口并不是继承于**`Collection`**接口**，**而是继承于**`Map`**接口的**。

<br>

![](https://static.pushy.site/files/Collection_Map_interface.png)

## 2. 迭代器

### 2.1 介绍

Iterator接口包含了4个方法：

```java
public interface Iterator<E> {
	boolean hasNext();
    E next();
    default void remove() {...}
    default void forEachRemaining(Consumer<? super E> action) {...}
}
```

Java中的迭代器查找操作与位置的变更是紧密相连的。查找一个元素的唯一方法时调用`next()`，而在执行查找操作的同时，迭代器的位置随之向前移动。

因此，Java迭代器认为是位于两个元素之前，当调用`next()`时，迭代器就会越过下一个元素，并返回刚刚越过的那个元素的引用。

![TIM截图20181006131712.png](https://i.loli.net/2018/10/06/5bb8471a69251.png)

### 2.2 使用

#### next

通过反复调用`next()`方法可以逐个访问集合中的每个元素，`hasNext()`返回的true代表集合中剩余的元素。

因此可以通过迭代器来遍历集合：

```java
List<String> mList = Arrays.asList("first", "second", "third");
Iterator<String> iter = mList.iterator();
while (iter.hasNext()) {
    System.out.println(iter.next());
}
```

#### forEachRemaining

还可以通过`Iterator`接口的方法来遍历：

```java
mList.iterator().forEachRemaining(System.out::println);
```

另外，**任何实现了Iterator接口的集合对象**都可以通过for-each方式来遍历元素：

```java
for (String e : mList) {
    System.out.println(e);
}
```

#### remove

Iterator接口的`remove()`方法将会删除上次调用`next()`方法返回的元素：

```java
iter.next();
iter.remove();
System.out.println(mList);  // [second, five]
```

需要注意的是，调用`remove()`方法之前必须调用`next()`方法，否则将会抛出异常。