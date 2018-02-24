## 1. 包的作用：

为了更好地组织类，JAVA提供了包机制。包是类的容器，用于分隔类名空间，JAVA包存在许多的优点，例如：

- 用于对类和接口分类，利于维护

- 提供访问权限保护

- 消除命名冲突（调用不同包中相同类名可加上包名区分）


## 2. 创建包：

我们可以通过`package`在文件的第一行声明来创建包，例如我们创建一个`com.pushy`包（`pushy`包为`com`包的子包）。

首先创建如下的目录和`Simple`类文件：

```
└─com
    └─pushy
            Simple.java
```

接着在`Simple.java`中编辑如下的内容：

```java
package com.pushy

public class Simple {
	public static void main(String() args) {
    	System.out.println("Welcom to com.pushy package");
    }
}
```

然后通过`javac`编译之后，就可以通过`com.pushy.Simple`来运行该类：

```
$ javac com/pushy/Simple.java
$ java com.pushy.Simple

Welcom to com.pushy package
```

## 3. 引入包：

访问包可以通过三种方法进行导入：

#### 导入所有类：

如果要导入某个包中的所有类，可以通过通配符来实现：

```java
import pack.*;
```

例如在如下的包结构当中：

```
├─p1
│      A.java
│
└─p2
       B.java
       C.java
```

在`p1`包的`B.java`类中，导入了`p2`包中所有的类，所以可以创建`p2`类中所有类的对象：

```java
package p1;
import p2.*;

class A {
  public static void main(String args[]){
     B obj = new B();
     C obj2 = new C();
     obj.run();
     obj2.run();
  }
}
```

#### 导入指定类：

与导入所有的类不同，通过`import pack.className`方式只导入指定的类：

```java
package p1;
import p2.B;

class A {
  public static void main(String args[]){
     B obj = new B();
     obj.run();
     // 无法实例化C类的对象
  }
}
```

#### 完全限定名

使用完全限定名通常用于两个包中具有相同的情况，需要加上包名来消除命名冲突。

例如在如下的包结构当中：

```
├─p1
│      A.java
│
├─p2
│      B.java
│      C.java
│
└─p3
       B.java
```

`p2`包和`p3`包同时存在了`B.java`类，所以使用完全限定名来同时使用这两个包：

```java
package p1;

class A {
  public static void main(String args[]){
     p2.B obj = new p2.B();
     p3.B obj2 = new p3.B();
     obj.run();
     obj2.run();
  }
}
```
