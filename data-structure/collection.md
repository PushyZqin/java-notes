## 1. Collection/Map

Java中的容器包括`Collection`和`Map`两种，`Collection`包括了`List`、`Set`的具体类，`Map`包括了`Map`的具体类。宏观来说，`Collection`和`Map`为容器的接口，`List`/`Set`/`Map`为实现接口的具体类，而`Collections`是一个工具类，提供了对集合进行排序，遍历等多种算法的实现。

在`List`、`Set`、`Map`存取元素时，有各自的特点和适用情况：

- `List` 以特定索引来存取元素，可以有重复的元素；

- `Set` 不能存放重复的元素；

- `Map` 为键值对映射容器，用来保存键值对映射。

而`List`等的具体类还包含了子类，例如`List`的子类有`ArrayList`/`LinkedList`，`Set`的子类有`HashSet`/`TreeSet`等等。

需要注意的一点是，`Map`**的具体类并不是继承于**`Collection`**接口**，**而是继承于**`Map`**接口的**。

<br>

![](https://static.pushy.site/files/Collection_Map_interface.png)