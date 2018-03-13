## 1. 指令

`JSP`指令用来设置整个`JSP`页面相关的属性，如网页的编码方式和网页使用的脚本语言。

```java
<%@ directive attribute="value" %>
```

### 1.1 Page指令

`Page`指令为容器提供当前页面的使用说明。

例如，设置在下面的`Page`指令当中，设置使用的脚本语言为`Java`，网页内容类型为`text/html`，编码方式为`UTF-8`：

```java
// 通过这样设置能让网页显示中文
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
```

通过`import`指令能够导入要使用的`Java`类：

```java
<%@ page import="java.util.*"%>
```

### 1.2 Include指令

`JSP`可以通过`include`指令来包含其他的文件，被包含的可以是`JSP`文件、`HTML`文件或者文本文件。

例如在`date.jsp`中显示当前的时间：

```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <p>
        当前时期为：<%= (new java.util.Date()).toLocaleString()%>
    </p>
</body>
</html>
```

然后在文件`directiveImport.jsp`中包含该文件：

```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<body>
    <%@include file="date.jsp"%>
</body>
</html>
```

## 2. 动作元素

与`JSP`指令不同，`JSP`动作元素在请求阶段起的作用，并且`JSP`动作元素是用`XML`语法写成的。利用`JSP`作用可以动态地插入文件、重用`JavaBean`组件，把用户重定向到另外的界面。

### 2.1 include

`<jsp:include>`动作元素用来包含静态和动态的文件，将制定的文件插入到正在生成的页面。

这与`include`指令不同，`include`指令是被转换成`Servlet`时候引入文件，而该`include`动作元素是在页面被请求的时候插入文件的。

```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<body>
	// page指定包含文件的地址，flush定义包含资源前是否刷新缓冲区
    <jsp:include page="date.jsp" flush="true" />
</body>
</html>
```

### 2.2 JavaBean动作元素

#### useBean

`<jsp:useBean>`可以用来加载一个将在`JSP`使用的`JavaBean`对象，例如：

```java
<jsp:useBean id="date" class="java.util.Date" />
<p> 现在的日期为： <%= date%> </p>
```

首先，了解一下什么是**JavaBean**：`JavaBean`就是一个普通的`Java`类，但是遵循着特定的写法，存在的好处是封装、重用、可读：

- 存在无参的构造函数

- 成员属性私有化

- 封装的属性如果需要对外操作，必须编写`public`类型的`setter`、`getter`方法

例如在`src/com`目录下创建一个`JavaBean`的类，存在`name`、`age`的`setter`以及`getter`的方法，以及一个空的构造函数：

```java
// 注意，不要在默认的包在创建该类，否则会报错
package com

public class StudentBean implements java.io.Serializable {
    private String name = null;
    private int age = 0;
    public StudentBean() {}
    public String getName() {
        return firstName;
    }
    public int getAge() {
        return age;
    }
    public void setName(String lastName){
        this.lastName = lastName;
    }
    public void setAge(int age) {
        this.age = age;
    }
}
```

这样就可以在`JSP`文件中加载并使用该`JavaBean`对象了：

```
<jsp:useBean id="students" class="com.StudentBean" />

<%
    students.setFirstName("Pushy");
    out.print(students.getFirstName());
%>
```

#### setProperty

`jsp:setProperty`用来设置已经实例化的`JavaBean`对象的属性：

```
<jsp:setProerty name="对象名称" property="属性名" param="参数名" value="值">
```

在如上的实例中，我们也可以使用`setProperty`动作元素来设置`StudentBean`的属性：

```java
<jsp:useBean id="students" class="com.StudentBean">
    <jsp:setProperty name="students" property="name" value="Pushy"/>
    <jsp:setProperty name="students" property="age" value="19"/>
</jsp:useBean>
```

我们还可以使用`<jsp:setProerty>`来将表单中的值设置到`JavaBean`对象当中，例如创建一个表单的`JSP`文件：

```
<form action="../action/form.jsp">
    <input type="text" name="name">
    <input type="text" name="age">
    <input type="submit" value="提交">
</form>
```

在`form.jsp`文件中将表单提交值设置到`javaBean`对象当中：

```java
<jsp:useBean id="person" class="com.StudenBean" scope="page"/>
// 内部自动获取到表单提交的请求参数值，并转化成相对应的int类型并设置到JavaBean对象当中
<jsp:setProperty name="person" property="age" />
// <jsp:setProperty name="person" property="*" />
<%
    out.println(person.getAge());
%>
```

另外，如果将`property`属性设置为`*`时，表示所有名字和`Bean`对象属性匹配的请求参数都将被传递到属性的`set`方法当中。也就是说，表单提交的请求参数`name`和`age`都将自动设置到`StudentBean`对象当中。

#### getProperty

`<jsp:setProerty>`用来提取指定`Bean`属性的值，转换成字符串并显示在页面当中，用法为：

```
<jsp:setProerty name="对象名称" property="属性名">
```

这样在获取`JavaBean`对象属性时，不需要通过`out.println(person.getAge());`调用对象中的`gettter`方法，而是直接通过`property`动作元素来获得相应的属性：

```
<jsp:getProperty name="students" property="name" />
```

### 2.3 formward

`<jsp:forward>`动作把请求转到`page`定义的`URL`地址，可以是一个`JSP`或者`Java Servlet`。

```java
<jsp:forward page="url" />
```

需要注意的是，这与重定向不同，这种转发请求页面的`URL`是不会改变的。


<br><br>

>参考资料
>
>[JSP第三篇【JavaBean的介绍、JSP的行为--JavaBean】](http://blog.csdn.net/hon_3y/article/details/54849497)
>
