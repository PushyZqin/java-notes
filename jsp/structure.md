## 1. 处理过程：

![](https://static.pushy.site/files/jsp_structure-fj3jf-3jfs3-j3jds.png)

## 2. 生命周期：

`jsp`中四个主要阶段和`servlet`生命周期非常相似，主要包含了：

### 2.1 编译阶段：

这一阶段`servlet`编译`servlet`源文件，生成`servlet`类。

当浏览器请求`JSP`页面时，`JSP`引擎会首先检查是否需要编译这个文件。如果这个文件没有被编译过，或者在上次编译后被更改过，则编译这个`JSP`文件。

编译包含了三个步骤：

- 解析`JSP`文件

- 将`JSP`文件转换为`servlet`

- 编译`servlet`


### 2.2 初始化：

服务器容器在载入`JSP`文件后，它会在为请求任何服务前调用`jspInit()`方法，如果需要自定义`JSP`的初始化执行的行为，则复写`jspinit()`就行：

```java
public void jspInit(){
  // 初始化代码
}
```

一般来讲程序只初始化一次，`servlet`也是如此。通常情况下可以在`jspInit()`方法中初始化数据库连接、打开文件和创建查询表。

### 2.3 执行：

这个阶段描述了`JSP`生命周期中一切与请求相关的交互行为，直到被销毁。

当`JSP`网页完成初始化后，`JSP`引擎将调用`_jspService()`方法。该方法需要将`HttpServletRequest`和`HttpServletResponse`作为它的参数：

```java
void _jspService(HttpServletRequest request,HttpServletResponse response)
{
   // 服务端处理代码
}
```

### 2.4 销毁：

该阶段描述了一个`JSP`网页从容器中被移除时发生的行为。

通常情况下，当执行释放数据库链接或关闭文件夹时复写该方法：

```java
public void jspDestroy()
{
   // 清理代码
}
```
