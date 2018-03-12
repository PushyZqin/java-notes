## 1. 指令：

`JSP`指令用来设置整个`JSP`页面相关的属性，如网页的编码方式和网页使用的脚本语言。

```java
<%@ directive attribute="value" %>
```

### 1.1 Page指令：

`Page`指令为容器提供当前页面的使用说明。

例如，设置在下面的`Page`指令当中，设置使用的脚本语言为`Java`，网页内容类型为`text/html`，编码方式为`UTF-8`：

```java
// 通过这样设置能让网页显示中文
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
```

### 1.2 Include指令：

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
<head>
    <title>Title</title>
</head>
<body>

    <%--通过指令来接在date.jsp文件--%>
    <%--在JSP文件被转换成servlet文件时引入文件--%>
    <%@include file="date.jsp"%>
    <%--通过动作元素来加载date.jsp文件--%>
    <%--在页面请求时插入文件--%>
    <jsp:include page="date.jsp" />
</body>
</html>
```



## 2. 动作元素：