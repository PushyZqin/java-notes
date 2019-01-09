# 1. 介绍

![LinkedList.png](https://i.loli.net/2019/01/05/5c3064ed5ca3d.png)

从上面的UML图中可以知道：`LinkedList `是一个继承于`AbstractSequentialList`的双向链表。它**也可以被当作堆栈、队列或双端队列进行操作。**

- 它实现 `List `接口，能对它进行队列操作。
- 它实现 `Deque `接口，即能将`LinkedList`当作双端队列使用。
- 它实现了`Cloneable`接口，即覆盖了函数`clone()`，能进行克隆。
- 它实现`java.io.Serializable`接口，这意味着`LinkedList`支持序列化，能通过序列化去传输。
- `LinkedList `是非同步的。

# 2. 双链表结构

JDK中的`LinkedList`是一种链表的数据结构，而且使用的是双链表的形式。节点的定义如下：

```java
class Node<E> {
    E item;            // 节点的元素
    Node<E> next;      // 后继节点
    Node<E> prev;      // 前驱节点

    /**
     * 构造方法，设置新节点的前驱和后继节点
     */
    Node(Node<E> prev, E element, Node<E> next) {
        this.item = element;
        this.next = next;
        this.prev = prev;
    }
}
```

并且，该`LinkedList`类与普通的单链表和双链表不同，成员变量中同时存在一个头结点`first`和一个尾节点`last`，以及一个链表的长度：

```java
public class LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable {
    
    transient int size = 0;  // 链表的长度
    
    transient Node<E> first;
    
    transient Node<E> last;
    
    public LinkedList() {
    }
}
```

这样做的目的是什么呢？这是因为，同时存在一个头结点和尾节点，可以根据索引值的范围，来决定从头节点向后遍历，还是从尾节点向前遍历。

`LinkedList`中存在一个`node(int index)`方法，它用来返回第`index`个节点。通过判断`index`与`size / 2`个大小，来决定遍历的方式：

```java
Node<E> node(int index) {
    /* size >> 1为移位操作，相当于size/2的操作 */
    if (index < (size >> 1)) { // 如果index小于size/2，那么从头开始向后遍历
        Node<E> x = first;
        for (int i = 0; i < index; i++)
            x = x.next;
        return x;
    } else { // 如果index大于size/2，那么从尾开始向前遍历
        Node<E> x = last;
        for (int i = size - 1; i > index; i--)
            x = x.prev;
        return x;
    }
}
```

# 3. 基本操作

## 3.1 插入

添加元素到链表的头部，实际上是调用内部的`linkFirst`的方法：

```java
public void addFirst(E e) {
    linkFirst(e);
}

/**
 * 添加元素到链头
 */
private void linkFirst(E e) {
    final Node<E> f = first; // 保存原来的头结点
    /* 创建新的节点，前驱设置为null，代表它会成为头结点 */
    final Node<E> newNode = new Node<>(null, e, f);
    first = newNode; // 将新的节点设置为头结点
    if (f == null)
        last = newNode;
    else
        f.prev = newNode; // 将之前的头结点前驱设置为新的节点
    size++;
    modCount++;
}
```

添加元素到链表的尾部，实际上是调用内部的`linkLast`方法：

```java
public void addLast(E e) {
    linkLast(e);
}

/**
 * 添加元素到链尾
 */
void linkLast(E e) {
    final Node<E> l = last; // 保存原来的尾节点
    /* 创建新的节点，后继设置为null，代表它会成为尾节点 */
    final Node<E> newNode = new Node<>(l, e, null);
    last = newNode; // 将新的节点设置为尾节点
    if (l == null)
        first = newNode;
    else
        l.next = newNode; // 将之前的尾节点的后继设置为新的节点
    size++; // 链表的长度+1
    modCount++; // 修改的次数+1
}
```

添加元素到某个位置，实际上是调用内部的`linkBefore`方法：

```java
public void add(int index, E element) {
    checkPositionIndex(index); // 检查索引是否越界或者不合理
    if (index == size) // 如果插入的是末尾，直接调用linkLast
        linkLast(element);
    else // 如果插入的是中间，则调用linkBefore
        linkBefore(element, node(index));
}

void linkBefore(E e, Node<E> succ) {
    final Node<E> pred = succ.prev; // 保存第i个节点的前驱结点
    /* 创建新的节点，前驱设置为第i-1个节点，后继设置为第i个节点 */
    final Node<E> newNode = new Node<>(pred, e, succ);
    succ.prev = newNode; // 将原先的第i个节点前驱设置为新的节点
    if (pred == null)
        first = newNode;
    else
        pred.next = newNode; // 将第i-1个节点的后继设置为新的节点
    size++;
    modCount++;
}
```

## 3.2 删除

直接调用`LinkedList`中的`remove`方法其实是调用`removeFirst()`删除第一个元素，而其内部实际上是调用`unlinkFirst`方法删除节点：

```java
/**
 * 删除一个节点
 */
public E remove() {
    return removeFirst();
}

/**
 * 删除第一个节点
 */
public E removeFirst() {
    final Node<E> f = first;
    if (f == null) // 检查是否存在头节点
        throw new NoSuchElementException();
    return unlinkFirst(f);
}

private E unlinkFirst(Node<E> f) {
    final E element = f.item;
    final Node<E> next = f.next; // 保留头节点的后继
    f.item = null;
    f.next = null;
    first = next; // 将头节点重新设置
    if (next == null)
        last = null;
    else
        next.prev = null; // 将头节点的后继的前驱设置为null，代表它现在是头节点
    size--; // 链表长度减1
    modCount++;
    return element; // 返回被删除的元素值
}
```

删除最后一个节点内部实际上是调用`unlinkLast`方法：

```java
public E removeLast() {
    final Node<E> l = last;
    if (l == null) // 检查是否存在尾节点
        throw new NoSuchElementException();
    return unlinkLast(l);
}

private E unlinkLast(Node<E> l) {
    final E element = l.item;
    final Node<E> prev = l.prev; // 保留尾节点的前驱
    l.item = null;
    l.prev = null;
    last = prev; // 将尾节点重新设置
    if (prev == null)
        first = null;
    else
        prev.next = null; // 将尾节点前驱的后继设置为null，代表它现在是尾节点
    size--; // 链表长度减1
    modCount++;
    return element; // 返回被删除的元素值
}
```

删除某个节点实际上调用的是`unlink`方法：

```java
/**
 * 删除某个节点
 * @param x 需要删除的节点
 * @return 被删除的元素
 */
E unlink(Node<E> x) {
    final E element = x.item;
    final Node<E> next = x.next; // 需要删除节点的后继
    final Node<E> prev = x.prev; // 需要删除节点的前驱

    if (prev == null) { // 代表删除的节点是头结点
        first = next;  // 直接将删除节点的后继设置为头结点
    } else {
        prev.next = next; // 将删除节点的前驱 next 设置为 删除节点的后继
        x.prev = null;
    }

    if (next == null) {  // 代表删除的是尾节点
        last = prev;  // 直接将删除节点的前驱设置为尾节点
    } else {
        next.prev = prev; // 将删除节点的后继的 prev 设置为 删除节点的前驱
        x.next = null;
    }

    x.item = null;
    size--;
    modCount++;
    return element;
}
```

## 3.3 修改节点的数值

设置第`index`个节点新的值，只需要通过`node`方法找到第`index`个节点，改变其`item`值即可：

```java
/**
 * 设置第index个节点为新的值
 */
public E set(int index, E element) {
    checkElementIndex(index);  // 检查数组是否越界
    Node<E> x = node(index);  // 查找到第index个节点
    E oldVal = x.item;
    x.item = element;  // 将节点的数据域设置为新的值
    return oldVal;
}
```

## 3.4 获取节点的值

获取第`index`个节点的值，需要通过`node`方法找到第`index`个节点，返回它的`item`值即可：

```java
public E get(int index) {
    checkElementIndex(index); // 检查数组是否越界
    return node(index).item;
}
```

## 3.5 其他操作

遍历所有节点：

```java
for (Node<E> x = first; x != null; x = x.next) {
    /* 对该节点 first 进行一些操作 */
}
```

清空链表，只需要遍历所有节点，将它们的前驱和后继以及数据域都设置为`null`，将头结点和尾节点也设置为`null`，并把链表长度归零即可：

```java
public void clear() {
    for (Node<E> x = first; x != null; ) {
        Node<E> next = x.next;
        x.item = null;
        x.next = null;
        x.prev = null;
        x = next;
    }
    first = last = null;  // 将头结点和尾节点设置为null 
    size = 0;  // 链表长度归零
    modCount++;
}
```

将链表转换为数组的操作也非常简单，将所有节点的值添加到新的数组即可：

```java
/**
 * 将链表中所有的元素添加到新的数组
 * @return
 */
public Object[] toArray() {
    Object[] result = new Object[size];
    int i = 0;
    /* 遍历所有的节点 */
    for (Node<E> x = first; x != null; x = x.next)
        result[i++] = x.item;
    return result;
}
```

