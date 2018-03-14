## 1. 内置对象

内置对象（隐式对象）是`JSP`容器为每个页面提供的`Java`对象，不需要预先声明就可以在脚本代码和表达式中随意使用。

`JSP`中包含了九大内置对象：

| 对象 | 描述 | 分类 |
|--------|--------|
|    request    |   HttpServletRequest类实例，处理客户的请求    | 输入输出对象 |
|   response     | HttpServletResponse类实例，设置反应行为 | 输入输出对象 |
|    application    | ServletContext类实例，实现多客户数据共享 | 通信控制对象 |
|   out     |   JspWriter类实例，用来在response对象写入内容  | 输入输出对象 |
|    session    |  HttpSession类实例，跟踪在各个客户端请求间的会话 | 通信控制对象 |
|    config    |  ServletConfig类实例，访问Servlet/JSP的初始化参数 | Servlet对象 |
|    pageContext    |  PageContext类实例，用来访问页面信息 | 通信控制对象 |
|    page    |    类似于Java类中的this关键字    | Servlet对象 |
|    Exception    |    Exception类的对象，代表发生错误的JSP页面中对应的异常对象    | 错误处理对象 |

## 2. request对象

### 2.1 定义

`request`对象是`HttpServletRequest`类的实例，每当客户端请求一个`JSP`页面时，`JSP`引擎就会制造一个新的`request`对象来代表这个请求。

并且，客户端的请求信息被封装到该`request`对象中，通过它可以获取到客户的需求，例如`HTTP`信息头，表单，`cookies`。

### 2.2 基本方法

- `Enumeration getHeaderNames()`方法返回所有的`HTTP`头名称的集合，返回一个枚举类型。

```java
<%
    // 枚举类型迭代HTTP请求的头信息
    Enumeration headersNames = request.getHeaderNames();
    while (headersNames.hasMoreElements()) {
        String paramName = (String) headersNames.nextElement();
        // 通过getHeader方法根据头名称获取响应的值：
        out.println(paramName + "：" + request.getHeader(paramName));
    }
%>
```

- `String getParameter()`方法用于获取`request`中请求的参数（表单或查询字符串），`Enumeration getParameterNames()
`则返回请求中所有的参数集合。

```java
<%
   String name = request.getParameter("name");
%>
```

- `Cookie[] getCookies()`方法返回客户端所有的`Cookies`的数组。

```java
<%
    Cookie[] cookies= request.getCookies();
    for (Cookie each : cookies) {
        out.println(each.getName() + " ： " +each.getValue());
    }
%>
```

<br>

>更多HttpServletRequest类方法可以看[JSP 客户端请求](http://www.runoob.com/jsp/jsp-client-request.html)

## 3. response对象

### 3.1 定义

`response`是`HttpServletResponse`对象，和`request`一样，服务器也会创建`response`对象，并将`JSP`容器处理后的结构传回到客户端。

通过该`response`对象，服务器可以进行设置`cookies`、设置`HTTP`状态码、重定向等功能。

### 3.2 基本方法

- `addHeader(name,value)`和`setHeader(name, value)`都可方法用于设置响应头，区别是如果添加的是已经定义的，`addHeader`将不会修改，而`setHeader`将会覆盖之前定义的值。

```java
<%
	// 将每隔10秒刷新一次：
    response.addHeader("refresh","10");
    response.setHeader("refresh","10")
    // getHeader()方法可用于读取响应头
    out.println(response.getHeader("token"));
%>
```

- `addCookie(Cookie cookie)`方法用于添加指定的`Cookie`对象至相应中。

```java
<%
    Cookie cookie = new Cookie("token","123456");
    // 设置cookie的过期时间，为60秒
    cookie.setMaxAge(60);
    response.addCookie(cookie);
%>
```

- `sendError(int sc, String msg)`向客户端返回状态码和错误信息。

```java
<%
    response.sendError(404,"The file was missed");
%>
```


<br>

>更多HttpServletRequest类方法可以看[JSP 服务器响应](http://www.runoob.com/jsp/jsp-server-response.html)

## 4. application对象

### 4.1 定义

`application`对象是`javax.servlet.ServletContext`的实例，当`Web`服务器启动时，`Web`服务器会自动创建一个`application`对象，将一直存在直到`Web`服务器关闭。

并且，在整个应用中运行过程只有一个`application`对象，也即所有访问该网站的客户都共享一个`application`对象。也就是说，不管那个客户来访问该网站，也不管客户访问该网站的哪个页面的文件，都能够对该网站的`application`对象进行操作，从而实现了多客户之间的数据共享。

### 4.2 基本方法

- `setAttribute`方法用来设置属性，指定属性名称和属性值。

```java
<%
    application.setAttribute(“userName”, "Pushy");
%>
```

- `getAttribute`方法用来根据属性名称回去属性值。

```java
<%= application.getAttribute("userName")%>

// 网页将显示内容:Pushy
```

- `getAttributeNames`方法用来获取所有属性的名称，并通过`getAttribute`来获取相应的值。

```java
Enumeration attrs = application.getAttributeNames();
while (attrs.hasMoreElements()) {
    String next = (String) attrs.nextElement();
    out.println(next+ "：" + application.getAttribute(next));
}
```

- `removeAttribute`方法用来根据属性删除对应的属性。

```java
<%
	application.removeAttribute("userName");
%>
```

### 4.3 点击量统计

```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<body>
    <%!
       int numbers = 0;
       public synchronized void countPeople() {
           numbers ++;
       }
    %>
    <%
        if (session.isNew()) {
            countPeople();
            // 返回numbers的字符串的表示形式:
            String str = String.valueOf(numbers);
            session.setAttribute("count",str);
        }
        application.setAttribute(session.getId(),Integer.toString(numbers));
    %>
    <%= session.getId()%>
    你是第 <%= (String) session.getAttribute("count")%> 个访问本站的人
</body>
</html>
```

## 5. session对象

### 5.1 定义

`session`对象是用来分别保存每一个用户信息的对象，以便于跟踪用户的操作状态，它保存在客户端，但是`session ID`是保存在客户端（浏览器）当中的。

例如，通过`String getID()`方法返回当前用户唯一的标识`ID`：

```java
<%= session.getId()%></p>

// 4FD0998074226E137C80E904FC00B372
```

### 5.2 基本方法

- 通过`setAttribute(name,value)`和`Object getAttribute(String name)`方法分别来设置指定的名称和值与返回指定名称的对象，并可以通过`removeAttribute(name)`方法来删除指定名称对象。

```java
<%
	session.setAttribute("userID","ABCD");
    // getAttribute方法的返回值是与名称绑定的对象类型
    userID = (String) session.getAttribute("userID");
    
    session.removeAttribute("userID");
%>
```

- `boolean isNew()`方法返回是否为一个新的客户端，或者客户端是否拒绝加入`session`。

```java
<%
	if (session.isNew()) {
    	out.println("欢迎来到我的网站")
    }
%>
```

## 6. pagecontext对象

### 6.1 定义

`pagecontext`对象用来代表整个`JSP`页面，它代表页面上下文，用于访问`JSP`之间的共享数据，使用`pageContext`可以访问`page`、`request`、`session`、`application`范围的变量。

### 6.2 基本方法

- `Object getBuildInObject()`方法返回当前页面的`Object`对象。例如，一个方法中需要传入多个内置对象将不方便，那么可以只传入`PageContext`，用它来调用其他的内置对象。

```java
import javax.servlet.jsp.PageContext

public void function(PageContext pageContext) throws IOException{
   // 得到session内置对象并调用其getId()方法
   pageContext.getSession().getId();
   // 得到out内置对象并调用其println方法
   pageContext.getOut().println("hello World");
}
```

<br>

>更多关于pagecontext对象的方法可以看[JSP pagecontext对象详解](http://www.51gjie.com/javaweb/831.html)

## 7. out对象

### 7.1 定义

`out`对象用来动态的向`JSP`页面输出字符流，把动态的内容转换成`HTML`形式展示，主要功能就是完成页面的输出操作的。

### 7.2 基本方法

- `getBufferSize()`获取缓冲区大小和`clearBuffer()`清除缓冲区内容用来对缓冲区进行操作。

```java
<%
	out.println(out.getBufferSize())
    // 将数据发送至客户端后，清除缓冲区中的内容
    out.clearBuffer()
    // 清除缓冲区中的内容，不将数据发送至客户端
    out.clear()
%>
```

- `println`、`print`用来显示各种数据类型的内容并将其显示在页面当中。

```java
<%
	out.println("Hello World")
%>
```

## 8. config对象

`config`对象代表当前`JSP`配置信息，实质上是`servletConfig`的一个实例，常用来获取`servlet`初始化参数。

<br>

>更多方法config对象方法可以看[JSP config对象详解](http://www.51gjie.com/javaweb/829.html)

## 9. exception对象

`exception`对象表示的就是`JSP`引擎在执行代码过程中抛出的种种异常，只有在`page`指令中设置`isErrorPage="true"`的页面中才能使用。

```java
<%@ page errorPage="/error.jsp" isErrorPage="true" %>
```

或者在`web.xml`中配置：

```xml
<error-page>
    <exception-type>java.lang.ArithmeticException</exception-type>
    <location>/error.jsp</location> 
</error-page>
```