## 1. 队列和双端队列

### 1.1 队列

队列可以有效地在尾部添加一个元素，在头部删除一个元素。在Java标准库中，由LinkedList实现：

```java
Queue<Integer> mQueue = new LinkedList<>();
mQueue.add(1);  // 添加尾部元素
mQueue.remove(); // 删除头部元素并返回该元素
mQueue.element(); // 返回头部元素
```

Queue接口提供了两组方法，上面这组在队列为空或为满时进行不合理的删除或添加的操作将会抛出异常，而下面这组将不会抛出异常：

```java
mQueue.offer(1);  // 添加尾部元素，队列满时返回false
mQueue.poll(); // 删除头部元素并返回该元素，队列空时返回null
mQueue.peek(); // 返回头部元素，队列空时返回null
```

### 1.2 双端队列

双端队列**可以有效地在头部和尾部同时添加或删除元素**，不支持在中间添加元素。在Java 6中引入了Deque接口，并由ArrayDeque和LinkedList实现：

```java
Deque<Integer> mDeque = new ArrayDeque<>();
mDeque.addFirst(1);
mDeque.addFirst(2);
mDeque.addFirst(3);

mDeque.addLast(4);  // [3, 2, 1, 4]

mDeque.removeFirst();  // [2, 1, 4]
```

和Queue一样，Deque接口也提供一系列不会抛出异常的方法。

## 2. 优先级队列

优先级队列中的元素可以按照任意的顺序插入，却总是按照排序的顺序进行检索。也就是说，不论何时调用`remove()`方法，总会获得当前优先级队列最小的元素。

实际上，它并没有对所有元素进行排除，而是使用了堆（可以自我调整的二叉树），对树执行添加和删除的操作，可以让最小的元素移动到根，而不用花费时间对元素进行排序。

```java
PriorityQueue<Integer> pq = new PriorityQueue<>();
pq.add(1);
pq.add(2);
pq.add(3);

System.out.println(pq);  // [1, 2, 3]
pq.remove(); // 删除优先级最小的元素 1
System.out.println(pq);  // [2, 3]
```

与TreeSet一样，一个优先级队列既可以保存实现了Comparable接口的类对象，也可以保存在构造器中提供的Comparator对象：

```java
// 通过Comparator对象设置根据字符串的长度来决定元素的优先级
PriorityQueue<String> pq2 = new PriorityQueue<>((o1, o2) -> o1.length() - o2.length());
pq2.add("Brooklynn");
pq2.add("Lindsey");
pq2.add("Veronica");

System.out.println(pq2);  // [Lindsey, Brooklynn, Veronica]
pq2.remove(); // 删除了优先级最小的元素 Lindsey
System.out.println(pq2);  // [Veronica, Brooklynn]
```

