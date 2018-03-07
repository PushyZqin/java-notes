## 1. Set：

从源码也可以看到，`Set`也是继承自`Collection`的接口的一个子接口，并且和`Collection`的接口几乎完全一样，没有额外添加新的方法，该接口同样定义了许多抽象的方法让实现该接口的具体类来实现：

```java
// Set.java部分源码代码：

public interface Set<E> extends Collection<E> {
    int size();
    boolean isEmpty();
    ...
```

`Set`的最大特性就是不允许其中存放的元素是重复的，这种不存入重复元素的功能是通过`equals`和`hashcode`方法进行实现的。

## 2. HashSet：

`HashSet`是基于`Hash`实现的，是无序的，但是支持快速储存和查找。

```java
public class HashSetDemo {
    public static void main(String[] args) {
        Set<Integer> hashSet = new HashSet<>();
        hashSet.add(1);
        hashSet.add(1);
        hashSet.add(2);
        hashSet.add(3);
        System.out.println(hashSet);
        // hashSet.add(1,1);  无法根据索引添加元素
        System.out.println(hashSet);
        // 使用迭代器来迭代HashSet：
        Iterator<Integer> iterator = hashSet.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
    }
}
```

如果要将对象储存在`HashSet`中，则在此之前，要先确保对象重写了`equals`和`hashCode`方法，这样才能比较两个对象是否相同，来确保不在`Set`中存在重复的元素。

如果没有定义的话，将会默认去自动调用`Object`类的`equals`方法，而该方法是用于判断对象的内存地址引用是不是同一个地址，并不能根据类的内容来判断两个对象是否相等。

也就是说，如果要让我们定义的类，通过判断对象的内容是否相等来确定对象之间是否相等，则需要重写`equals()`方法，如果要确保`Set`中不存在重复的元素，则需要重写`hashCode()`方法。

首先先定义一个`Student`类，并重写该类的`equals`方法（默认存在于`Object`类中）和`hashCode`方法：

```java
class Student {
    private int age;
    private String name;
    public Student (int age,String name) {
        this.age = age;
        this.name = name;
    }
    // 覆盖equals方法，让Student通过判断对象的内容是否相等来确定对象之间是否相等：
    @Override
    public boolean equals(Object obj) {
        if (this == obj)
            return true;
        if (obj == null)
            return false;
        if (getClass() != obj.getClass())
            return false;
        Student other = (Student) obj;
        if (age != other.age)
            return false;
        if (name == null) {
            if (other.name != null)
                return false;
        } else if (!name.equals(other.name)) {
            return false;
        }
        return true;
    }
    // 覆盖 hashCode 方法，让HashSet中只存放一个通过equals判断后重复的对象：
    @Override
    public int hashCode() {
    	// 返回一个区别于不同对象的值：
    	int result = age % 10;
        return result;
    }
}
```

在`Main`方法中，创建一个`hashSet`对象，并往里添加两个相同内容的`Student`对象，这样，`hashSet`对象中只能存在一个相同的`Student`对象：

```java
public class EqualsMethod {
    public static void main(String[] args) {
        HashSet<Student> hashSet = new HashSet<>();
        Student s1 = new Student(19,"Pushy");
        Student s2 = new Student(19,"Pushy");
        hashSet.add(s1);
        hashSet.add(s2);
        System.out.println(hashSet);
        // 调用 equals方法，返回true说明s1、s2为相同的对象
        System.out.println(s1.equals(s2));
    }
}

// [collections.set.Student2@49e4a8d]
// true
```

## 3. TreeSet：

`TreeSet`是基于[红黑树](https://baike.baidu.com/item/%E7%BA%A2%E9%BB%91%E6%A0%91/2413209?fr=aladdin)结构实现的，是有序的，但是查找效率较低。

### 3.1 自然排序：

在`Integer`、`String`等数据类型的类中，都实现了`Comparable`接口：

```java
// Integer.java 部分源码代码
public final class Integer extends Number implements Comparable<Integer> {
	...
}
```

说明`Comparable`是一个对象（就如`Integer`对象）本身就支持自比较所需要的接口，此接口对实现它的每个类的对象的元素自身具备比较性，这种排序称为类的自然排序（默认排序方式），类实现的抽象方法`compareTo`为它的自然比较方法。

例如我们先定义一个`Teacher`类，然后实现`Comparable`接口并重写`compareTo`方法，在`compareTo`方法中定义了排序的规则：

```java
class Teacher implements Comparable<Teacher>{
    int age;
    String name;
    @Override
    public int compareTo(Teacher o) {
        if (this.age > o.age) {
            return 1;
        } else if (this.age == o.age) {
            return 0;
        } else {
            return -1;
        }
    }
    public Teacher (int age, String name) {
        this.age = age;
        this.name = name;
    }
}
```

然后在`main`实例化`Teacher`对象并将它们添加到`TreeSet`对象当中，当进行迭代时，迭代顺序将会按照上面在`compareTo`方法中定义的排序规则，即按照年龄进行升序排序：

```java
public class TreeSetNaturalSort {
    public static void main(String[] args) {
        TreeSet<Teacher> treeSet = new TreeSet<>();
        treeSet.add(new Teacher(32,"A"));
        treeSet.add(new Teacher(28,"B"));
        treeSet.add(new Teacher(40,"C"));
        for (Teacher each : treeSet) {
            System.out.println(each.age);
        }
    }
}

// 28
// 32
// 40
```

### 3.2 客户化排序：

我们都知道，`Set`迭代顺序和插入的顺序并不相同（与`List`不同），是根据值进行升序排列：

```java
public class TreeSetSort {
    public static void main(String[] args) {
        TreeSet<Integer> treeSet = new TreeSet<>();
        treeSet.add(2);
        treeSet.add(1);
        treeSet.add(3);
        for (Integer each : treeSet) {
            System.out.println(each);
        }
    }
}

// 1
// 2
// 3
```

这是因为上述代码在实例化`TreeSet`对象时，调用了默认的构造器（没有传入`comparator`参数），当这个参数为`null`时，则默认使用元素的自然排序顺序。

但是如果不想使用自然排序，并且想让排列的顺序按照值降序排序，只需要在`TreeSet`的构造器中创建实现`Comparator`接口的类的对象，并在`compare`重写方法中添加排序的规则：


```java
public class TreeSetSortByDesc {
    public static void main(String[] args) {
        TreeSet<Integer> treeSet = new TreeSet<>(new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
            	// 当o2小于o1时返回-1,等于时返回0,大于时返回1：
                return o2.compareTo(o1);
            }
        });
        treeSet.add(2);
        treeSet.add(1);
        treeSet.add(3);
        for (Integer each : treeSet) {
            System.out.println(each);
        }
    }
}

// 3
// 2
// 1
```

这就是客户化排序，也就是说当元素本身并不具备比较性（没有实现`Comparable`接口），或者是具备比较性，确实不需要的，比如例子默认是升序，而实际却需要降序操作，就可以在集合初始化的构造函数中创建一个比较器`Comparator`，并且在重写`compare()`方法中指定需要的排序规则。

两者对比来看，`Comparable`为静态绑定排序，是对象提供的默认排序方式；`Comparator`为动态绑定排序，可以理解为自定义的排序方式。


## 4. LinkedHashSet：