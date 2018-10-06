## 1. Map：

从源码来看，`Map`为一个接口，接受一个`key-value`结构的泛型数据：

```java
// Map.java部分源码代码

public interface Map<K,V> {
	int size();
    boolean isEmpty();
    ...
}
```

`Map`是键值对映射容器，特性是用来保存键值对映射的。类似于`Python`中字典数据类型。实现`Map`接口的具体类有：`HashMap`、`TreeMap`、`LinkedHashMap`、`ConcurrentHashMap`。


## 2. HashMap：

### 2.1 基本使用

`HashMap`是基于`hash`实现的，因此访问的速度很快，但是失去了有序性。并且`HashMap`最多只允许一条记录的键为`null`（多余的会被覆盖），允许多条记录的值为`null`。

这与传统的数据类型`HashTable`类似，但是`HashTable`是支持同步的，线程安全的，并且不支持插入`null`值。

两种数据类型类似，都是在哈希表中存储键/值对。当使用一个哈希表，要指定用作键的对象（`key`），以及该键要链接到的值（`value`)。

```java
public class HashMapDemo {
    public static void main(String[] args) {
        Map<String,Integer> hashMap = new HashMap<String,Integer>();
        hashMap.put("Chinese",99);
        hashMap.put("English",98);
        hashMap.put("Math",100);
        // 使用get()方法通过键来映射出相对应的值：
        System.out.println("The math grade is :" + hashMap.get("Math"));
        }
    }
}
```

### 2.2 映射视图

集合框架不认为Map本身是一个集合，不过，可以得到Map的视图：

```java
Set<K> keySet();  // 键集
Collection<V> values();  // 值集
Set<Map.Entry<K,V>> entrySet();   // 键/值集
```

因为Set实现了Collection接口，因此可以通过`for-each`遍历一个Map的所有键（或者值）：

```java
for (String key : map.keySet()) {
    System.out.print(key);
}
```

如果想同时查看键和值，可以通过枚举条目来避免查找值：

```java
for (Map.Entry<String,Integer> entry : map.entrySet()) {
    String k = entry.getKey();
    String v = entry.getvalue();
    ...
}
```

现在，还可以通过更加优雅的`foreach`的方法来遍历键和值：

```java
map.forEach((k,v) -> {
    System.out.println(k, v);
});
```

## 3. TreeMap：

`TreeMap`是基于红黑树实现的，访问速度较慢，但是具有有序性。它能够把它保存的记录默认根据键排序（升序），与`TreeSet`相同，也可以在构造函数中创建比较器`Comparator`对象，进行客户化排序。

```java
import java.util.Map.Entry;

public class TreeMapDemo {
    public static void main(String[] args) {
        TreeMap<Double,Integer> treeMap = new TreeMap<>();
        treeMap.put(3.0,1);
        treeMap.put(1.0,3);
        treeMap.put(2.0,2);
        for (Entry<Double,Integer> entry : treeMap.entrySet()) {
            System.out.println(entry.getKey() + "：" + entry.getValue());
        }
    }
}

// 1.0：3
// 2.0：2
// 3.0：1
```

可以从控制台的内容看到，当迭代`TreeMap`时，默认是根据`key`升序的排列顺序，如果要换为降序排列，可以看：[TreeMapSortKeyByDesc.java](https://github.com/PushyZqin/java-demo/blob/master/data-structure/src/collections/map/TreeMapSortKey.java)。

但是如果说要对`Map`中的`value`进行排序，则需要使用到`Collections`中的`sort`方法来进行排序操作：

```java
public class TreeMapSortValue {
    public static void main(String[] args) {
        TreeMap<Double, Integer> map = new TreeMap<Double,Integer>();
        map.put(3.0,1);
        map.put(1.0,3);
        map.put(2.0,2);
        // 通过ArrayList的构造函数来将map.entrySet()转换成list：
        List<Map.Entry<Double,Integer>> list = new ArrayList<Map.Entry<Double,Integer>>(map.entrySet());
        // 然后通过比较器Comparator来对value值实现排序：
        Collections.sort(list, new Comparator<Map.Entry<Double, Integer>>() {
            @Override
            public int compare(Map.Entry<Double, Integer> o1, Map.Entry<Double, Integer> o2) {
                return o1.getValue().compareTo(o2.getValue());
            }
        });

        for (Map.Entry<Double,Integer> mapping:list) {
            System.out.println(mapping.getKey() + "：" + mapping.getValue());
        }
    }
}

// 3.0：1
// 1.0：3
// 2.0：2
```

## 4. LinkedHashMap：

`LinkedHashMap`是`HashMap`的一个子类，具有`HashMap`的查找效率。

但是`LinkedHashMap`使用了链表结构来维护了记录的插入顺序，在用`Iterator`遍历`LinkedHashMap`时，先得到的记录肯定是先插入的：

```java
public class LinkedHashMapDemo {
    public static void main(String[] args) {
        LinkedHashMap<Double,Integer> linkedHashMap = new LinkedHashMap<>();
        linkedHashMap.put(3.0,1);
        linkedHashMap.put(1.0,3);
        linkedHashMap.put(2.0,2);
        for (Entry<Double,Integer> entry : linkedHashMap.entrySet()) {
            System.out.println(entry.getKey() + "：" + entry.getValue());
        }
    }
}

// 3.0：1
// 1.0：3
// 2.0：2
```

除此之外，也可以在构造时带参数，按照访问次序排序。

### 5. ConcurrentHashMap：

线程安全 `Map`，不涉及类似于 `HashTable` 的同步加锁。