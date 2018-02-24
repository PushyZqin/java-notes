# First see Java

## 一、安装配置：

在此之前先了解下`Java`中的名词：

![](http://pan.pushy.site/_uploads/files/java_name.png)

首先是`java`的安装和配置，下载`JDK`，添加环境变量即可。`IDE`使用[Eclipse](http://mirrors.ustc.edu.cn/eclipse/oomph/epp/oxygen/M5/)或者[intellij IDEA](https://www.jetbrains.com/idea/)

如下有详细的安装教程：

>[Java 开发环境配置](http://www.runoob.com/java/java-environment-setup.html)


### 1. Hello world：

以下是最简单的`Hello world语法`：

首先声明一个`HelloWorld`类，其文件名必须与类名一致：`HelloWorld.class`，定义一个`main`函数，打印出`Hello world`
```java
public  class  HelloWorld  {  
	public  static  void  main(String  []args)  {  
		System.out.println("Hello World");
	}  
}
```
其中`public  static  void  main(String  []args)`理解为：

```
public:权限修饰符，权限最大。
static:随着MianDemo类的加载而加载，消失而消失。并且用static修饰的代码块表示静态代码块，当Java虚拟机（JVM）加载类时，就会执行该代码块
void:  没有返回值
main: 函数名，jvm识别的特殊函数名
(String[] args):定义了一个字符串数组参数
```

### 2. 规则和规范：

- 类名必须和文件名保持一致！！！

- 字符串必须使用`""`

- 类名、方法名、变量名采用第一个单词小写，从第二个单词开始首字母大写的命名规范；





