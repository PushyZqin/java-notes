## 1. List：

从源码来看，`List`为继承自`Collection`的接口，接受一个泛型的类型，在这个接口中定义许多抽象方法，让继承自他的子类实现：

```java
// List.java部分源码代码：

public interface List<E> extends Collection<E> {
	boolean isEmpty();
    boolean contains(Object o);
    Iterator<E> iterator();
    ...
}
```

`List`的特征是其元素以线性方式储存，集合中可以存放重复的对象。实现`List`接口的具体类有：`ArrayList`和`LinkedList`。

## 2. ArrayList：

该具体类实现了`List`的接口，实现了可变大小的随机数组，是无序的，使得随机访问的速度快，但是插入与删除元素的效率低。

`ArrayList`与传统的数据类型`Vector`十分相似，`ArrayList`与后者的区别在于`Vector`是同步的，是线程安全的，并且访问速度较慢。

```java
public class ArrayListDemo {
    public static void main(String[] args) {
        List<String> list = new ArrayList<String>();
        // 添加元素：
        list.add("Hello");
        list.add("World");
        // 通过get方法索引得到数组的值：
        System.out.println(list.get(1));
        // set方法修改数组中的值，第一个参数为索引值，第二个为修改后的值：
        list.set(1,"java");
        System.out.println(list);
        // remove 方法索引删除列表中的值：
        list.remove(1);
        System.out.println(list);
        // contains方法用于判断元素是否在数组内：
        if (list.contains("World")) { System.out.println("该数组包含World元素"); }
        else {System.out.println("该数组未包含World元素");}
        // 通过Iterator迭代器迭代数组中的值：
        Iterator<String> ite = list.iterator();
        while (ite.hasNext()) {
            System.out.println(ite.next());
        }
    }
}

// World
// [Hello, java]
// [Hello]
// 该数组未包含World元素
// Hello
// World
```

## 3. LinkedList：

该具体类也实现了`List`接口，基于双向循环链表实现的顺序数组，是有序的，使得随机访问的速度慢，但是插入与删除的效率低，另外`LinkedList`还可以用作栈、队列和双端队列。

```java
public class LinkedListDemo {
    public static void main(String[] args) {
        LinkedList<String> linkedList = new LinkedList<>();
        // 增加元素：
        linkedList.add("one");
        linkedList.add(1,"two"); // 按指定索引增加元素
        linkedList.add("three");
        System.out.println(linkedList);
        // 更新元素值：
        linkedList.set(1,"five");
        System.out.println(linkedList);
        // 获得元素值：
        System.out.println("The index 1 value is :" + linkedList.get(1));
        // 一些队列操作：
        System.out.println(linkedList.peek()); // 获取但不移除此列表的头
        linkedList.poll(); // 获取并移除列表的头
        System.out.println(linkedList);
        // 循环遍历操作：
        Iterator<String> iterator = linkedList.iterator();
        while (iterator.hasNext()) {
            System.out.print(iterator.next() + " ");
        }
        // 使用for增强循环进行遍历：
        for (String each : linkedList) {
            System.out.print(each + " ");
        }
    }
}
```