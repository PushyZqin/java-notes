## 1. Enumeration（枚举）：

枚举定义了一种从数据结构中取回连续元素的方法，与`iterator`（迭代器）功能类似，并被迭代器所替代。

`Enumeration`定义了两个方法：

- `hasMoreElements()`用来检测是否还有剩余的元素，有则返回`true`；

- `nextElement()`用于获得下一个元素

例如我们可以使用枚举类型来迭代`Hashtable`返回的哈希表中键的枚举：

```java
public class HashTableDemo {

    public static void main(String[] args) {
        Hashtable balance = new Hashtable();
        balance.put("Zara", new Double(3434.34));
        balance.put("Mahnaz", new Double(123.22));
		// 创建一个枚举对象，用来接收balance返回此哈希表中的键的枚举：
        Enumeration names = balance.keys();
        // 通过枚举类型进行迭代：
        while (names.hasMoreElements()) {
            String str = (String) names.nextElement();
            System.out.println(str);
            balance.get(str);
        }
    }
}
```

需要注意的是：`Enumeration`迭代器只能遍历`Vector`、`Hashtable`这种传统的集合，应该使用`iterator`迭代器来替代：

```java
Iterator<String> ite = balance.keySet().iterator();
while (ite.hasNext()) {
    System.out.println(ite.next());
}
```

## 2. Vector（向量）：

`Vector`实现了一个动态数组，主要用在事先不知道数组的大小，或者只是需要一个可以改变大小的数组的情况，因为可以通过`addElement()`来增加大小：

```java
import java.util.*;

public class VectorDemo {
    public static void main(String[] args) {
        // 创建指定大小（第一个参数）和增量（第二个参数,表示向量每次增加的元素数目）的向量：
        Vector v = new Vector(3,2);
        v.add(0,1); // 在指定位置插入元素
        v.addElement(2); // 将指定的组件添加到此向量的末尾，将其大小增加 1
        
        Enumeration etn = v.elements();
        while (etn.hasMoreElements()) {
            System.out.println(etn.nextElement());
        }
    }
}
```

## 3. Stack（栈）：

`Stack`为`Vector`的一个子类，实现了一个标准的后进先出的栈：

```java
import java.util.*;

public class StackDemo {
    public static void showPush(Stack<Integer> st,int a) {
        st.push(new Integer(a)); // 入栈
        System.out.println("stack： " + st);
    }
    public static void showPop(Stack<Integer> st) {
        st.pop(); // 出栈
        System.out.println("stack： " + st);
    }

    public static void main(String[] args) {
    	// 创建Stack对象，为一个泛型类：
        Stack<Integer> st = new Stack<Integer>();
        showPush(st,2);
        System.out.println("The top is ：" + st.peek()); // 查看堆栈顶部对象
        showPop(st);
    }
}
```

## 4. Hashtable（哈希表）：

`Hashtable`在哈希表中储存键与对应的值，当使用这个`Hashtable`对象时，通过键值映射到键对应的值上。

```java
public class HashTableDemo {

    public static void main(String[] args) {
        Hashtable balance = new Hashtable();
        balance.put("Zara", new Double(3434.34));  // 将键和值添加到哈希表
        balance.put("Mahnaz", new Double(123.22));
        
		// 创建一个枚举对象，用来接收balance返回此哈希表中的键的枚举：
        Enumeration names = balance.keys();
        // 通过枚举类型进行迭代哈希表中所有键的集合：
        while (names.hasMoreElements()) {
            String str = (String) names.nextElement();
            System.out.println(str);
            // 通过键得到对应的值：
            balance.get(str);
        }
    }
}
```


## 5. Properties（属性）：

`Properties `继承于 `Hashtable`，表示一个持久的属性集，**属性列表中每个键及其对应值都是一个字符串**。

```java
public class PropertiesDemo {
    public static void main(String[] args) {
        Properties capitals = new Properties();
        capitals.put("Illinois","Springfield");
        capitals.put("Missouri","Jefferson City");
        
        Set states = capitals.keySet(); // 返回一个集合对象
        Iterator itr = states.iterator();
        // 通过iterator来迭代集合，hasNext()来检查迭代器是否还有剩余的元素：
        while (itr.hasNext()) {
            String str = (String) itr.next(); // 获取到属性中的key值
            // 通过key值读取属性：
            System.out.println(capitals.getProperty(str));
        }
    }
}
```