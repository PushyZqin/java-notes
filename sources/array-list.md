# 1. 介绍

![ArrayList.png](https://i.loli.net/2019/01/06/5c31a67704652.png)

从上面的UML图中可以知道：它继承了`AbstractList`，并实现了`List`等接口。它是一个数组队列，相当于动态数组，与Java内置的`Array`不同，它的容量能够动态地增长。

- 它继承了`AbstractList`，实现了`List`，它是一个数组队列，可以执行添加、删除、修改、遍历等基本操作。
- 它实现了`RandomAccess`接口，即**表明它具有能够随机访问的功能**，这个功能是`LinkedList`不具有的。这是因为`ArrayList`底层是由数组来实现，而数组天生就具有通过下标快速获取到元素对象的特性。
- 它实现了`Cloneable`接口，即覆盖了函数`clone()`，能进行克隆。
- 它实现`java.io.Serializable`接口，这意味着`LinkedList`支持序列化，能通过序列化去传输。
- `ArrayList`不是线程安全的，如果需要（同步的功能），可以使用`Vector`结构来代替。

# 2. 动态扩展

动态扩展是`ArrayList`的一大特性，弥补了数组需要指定容量并且无法进行扩展的缺陷，具体是怎么实现的呢？基本的方法有以下的四个：

- `calculateCapacity`：判断当前数组是否为空数组（即使用无参构造方法，没有指定默认的容量大小），如果是，则对默认容量（`10`）与`minCapacity`取最大值。
- `ensureCapacityInternal`：调用`ensureExplicitCapacity`方法来判断是否需要扩展。
- `ensureExplicitCapacity`：判断`minCapacity`和数组大小，来决定是否扩展数组。
- `grow`：将数组的容量扩展至原来的`1.5`倍。

```java
private static int calculateCapacity(Object[] elementData, int minCapacity) {
    /* 如果当前数组为空，在第一次添加时，将会设置为默认大小：10 */
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        return Math.max(DEFAULT_CAPACITY, minCapacity);
    }
    return minCapacity;
}

private void ensureCapacityInternal(int minCapacity) {
    ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
}

private void ensureExplicitCapacity(int minCapacity) {
    modCount++;
    /* 当 minCapacity 大于 当前数组的长度时，扩展数组 */
    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
}

/**
* 扩展数组，将数组扩大增加原先数组的1/2容量，即原来的1.5倍
*/
private void grow(int minCapacity) {
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + (oldCapacity >> 1);  // oldCapacity / 2 
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)  // 判断是否比规定最大容量还大
        newCapacity = hugeCapacity(minCapacity);
    // 动态地扩展数组
    elementData = Arrays.copyOf(elementData, newCapacity);
}

private static int hugeCapacity(int minCapacity) {
    if (minCapacity < 0) // overflow
        throw new OutOfMemoryError();
    return (minCapacity > MAX_ARRAY_SIZE) ?
        Integer.MAX_VALUE : MAX_ARRAY_SIZE;
}
```

在扩展容量时，使用到了`Arrays.copyOf(T[] original, int newLength)`方法：第一个参数为复制对象的数组，第二个参数则为要建立的新数组长度。如果新的数组的长度超过原数组的长度，则保留数组的默认值（如果数组存放类型是`int`，则为`0`）。例如：

```java
int[] arr = {1, 2, 3};
int[] newArr = Arrays.copyOf(arr, arr.length * 2);  // 扩大至原来数组的2倍

// newArr = [1, 2, 3, 0, 0, 0]
```

需要注意的是，`copyOf`返回的是复制的新数组，并不会影响到原先的数组，因此需要将返回的数组重新赋值给`elementData`。

# 3. 构造方法

## 3.1 无参构造

`ArrayList`无参构造函数会将内置的数组赋值为空数组，以便再第一次`add`时扩展数组的容量：

```java
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

public ArrayList() {
    /* 当前的size == 0，但是在下一次add时，数组将会自动扩展为10 */
    this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
}
```

## 3.2 指定容量

`ArrayList`允许构造方法传入一个自定义的初始容量大小，如果该值大于`0`，那么将会以该大小初始化`elementData`数组：

```java
public ArrayList(int initialCapacity) {
    if (initialCapacity > 0) {
        this.elementData = new Object[initialCapacity];
    } else if (initialCapacity == 0) {
        this.elementData = EMPTY_ELEMENTDATA;
    } else {
        throw new IllegalArgumentException("Illegal Capacity: "+
                                           initialCapacity);
    }
}
```

## 3.3 指定集合

如果构造方法指定一个集合对象：当该对象元素长度不为`0`时，将会该集合的所有元素复制到内置数组当中；否则将`elementData`设置为空的数组：

```java
public ArrayList(Collection<? extends E> c) {
    elementData = c.toArray();
    if ((size = elementData.length) != 0) {
        if (elementData.getClass() != Object[].class)
            elementData = Arrays.copyOf(elementData, size, Object[].class);
    } else {
        this.elementData = EMPTY_ELEMENTDATA;
    }
}
```

# 4. 基本操作

## 4.1 获取

获取与`LinkedList`相比，由于数组的特性有着极大的速度优势，很快地通过索引获取到数组对应下标的元素：

```java
public E get(int index) {
    rangeCheck(index);   // 判断索引是否越界
    return elementData(index);
}

/**
 * 获取数组的第index个元素，并做类型的强制转换
 */
E elementData(int index) {
    return (E) elementData[index];
}
```

## 4.2 修改

修改的操作非常简单，只需要设置数组的对应下标的值即可：

```java
public E set(int index, E element) {
    rangeCheck(index);  // 判断索引是否越界

    E oldValue = elementData(index);
    elementData[index] = element;  // 设置新的值
    return oldValue;
}
```


## 4.3 插入

了解了动态扩展的实现之后，插入的操作的`add()`就显得非常简单了，我们只需要在每次插入时确保有足够的容量插入即可：

```java
public boolean add(E e) {
    /* 确保当前大小+1之后仍然有空间可以存储，防止数组会发生越界 */
    ensureCapacityInternal(size + 1);
    
    elementData[size++] = e;  // 在末尾插入元素
    return true;
}
```

如果插入到指定位置的话，还需要做的操作是通过`arraycopy`方法将插入位置的所有元素后移：

```java
public void add(int index, E element) {
    rangeCheckForAdd(index);

    /* 确保当前大小+1之后仍然有空间可以存储，防止数组会发生越界 */
    ensureCapacityInternal(size + 1);
    /* 通过arraycopy的方法，将需要插入的位置（index）后面的元素统统往后移动一位 */
    System.arraycopy(elementData, index, elementData, index + 1,
                     size - index);
    elementData[index] = element;  // 插入元素
    size++;
}

/* 检查插入的位置是否不合理 */
private void rangeCheckForAdd(int index) {
    if (index > size || index < 0)
        throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
}
```

通过阅读源码，我们需要掌握算法的技巧，这里就使用`arraycopy`方法的技巧来移动元素。实现的原理是什么呢？该方法的作用实际上的作用是：**将某个数组的某一段字节数据复制到另一个数组中**。但是，我们却可以利用这个作用来实现元素的后移。下边是`aarrycopy`方法的定义：

```java
/**
* @param      src      源数组
* @param      srcPos   源数组要复制的起始位置
* @param      dest     目标数组
* @param      destPos  目标数组防止的起始位置
* @param      length   复制的长度
*/
public static native void arraycopy(Object src,  int srcPos,
                                    Object dest, int destPos,
                                    int length);
```

怎么实现呢？我们指定数组源数组和目标数组都为`arr`，这样复制的操作都是在一个数组上进行。然后指定`srcPos`和`destPos`分别指定为`index`和`index+1`，代表将`arr`的第`index`元素复制到`index+1`上，将覆盖`arr`原先第`index+1`个元素，至于复制（移动）多少个，由`numMoved`来决定：

```java
int index = 1;      //  开始移动的位置，即我们要插入元素的位置
int numMoved = 2;   //  移动的个数
int[] arr = {1, 2, 3, 4, 5};

// 将arr从index~(index+numMoved-1)元素都向后移动一位
System.arraycopy(arr, index, arr, index + 1, numMoved);

// arr = [1, 2, 2, 3, 5]
```

可以看到，与`LinkedList`只需要改变结点后继的引用不同，`ArrayList`则需要将元素一个一个的移动，在添加和删除的操作上，`ArrayList`的速度是明显低于`LinkedList`的。

## 4.4 删除

删除`index`下标元素需要做的操作一样是通过`arraycopy`方法，将要删除位置后的所有元素前移：

```java
public E remove(int index) {
    rangeCheck(index);
    modCount++;
    E oldValue = elementData(index);  // 获取到第index上的元素
    int numMoved = size - index - 1;
    if (numMoved > 0)
        /* 通过arraycopy方法，将指定位置（index）上的元素都往前移动一位 */
        System.arraycopy(elementData, index+1, elementData, index,
                         numMoved);
    elementData[--size] = null;   // 防止对象游离，帮助垃圾回收器回收
    return oldValue;
}
```

删除指定元素则比较麻烦，需要遍历数组找到该元素的下标，然后使用与`remove(int index)`类似的方法删除元素：

```java
// 遍历数组找到对象o的在数组中的下标
public boolean remove(Object o) {
    if (o == null) {
        for (int index = 0; index < size; index++)
            if (elementData[index] == null) {
                fastRemove(index);
                return true;
            }
    } else {
        for (int index = 0; index < size; index++)
            if (o.equals(elementData[index])) {
                fastRemove(index);
                return true;
            }
    }
    return false;
}

private void fastRemove(int index) {
    modCount++;
    int numMoved = size - index - 1;
    if (numMoved > 0)
        // 将数组前移
        System.arraycopy(elementData, index+1, elementData, index,
                         numMoved);
    elementData[--size] = null;  // 防止对象游离，帮助垃圾回收器回收
}
```

## 4.5 其他操作

清空`ArrayList`所有的元素只需要遍历将它们的值都设置`null`即可， JVM的垃圾回收器会自动回收内存的：

```java
/**
* 清除所有的元素
*/
public void clear() {
    modCount++;
    for (int i = 0; i < size; i++)
        elementData[i] = null;
    size = 0;
}
```

