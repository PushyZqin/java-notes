## 1. 基本语法：

**脚本程序**中可以包含任何量的`Java`语句、变量、方法和表达式，只要它们在脚本语言中是有效的。

例如可以如下创建一个脚本程序的代码片段：

```java
<%
	out.println("Your IP address is " + request.getRemoteAddr());
%>
```

如果需要**声明一个变量**，则需要在使用`<%! ... %>`声明语句：

```java
<%! int i = 0; %>
<%! int a, b, c; %>
```

**表达式**中包含的脚本语言的表达式，先会被转换成`String`，然后插入到表达式出现的地方：

```
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>菜鸟教程(runoob.com)</title>
</head>
<body>
<p>
   今天的日期是: <%= (new java.util.Date()).toLocaleString()%>
</p>
</body>
</html>
```

## 2. 控制语句：

在`JSP`中，可以使用`Java`中的循环结构语句，包括判断语句和循环语句等。

### 2.1 if 语句：

```java
<%! int day = 3; %>

<%
    if (day == 6 || day ==7 ) {
        out.println("今天是周末");
    } else {
        out.println("今天不是周末");
    }
%>
```

### 2.2 swith 语句：

```java
<%! char grade = 'A'; %>

<%
    switch (grade) {
        case 'A':
            out.print("优秀");
            break;
        case 'B':
            out.print("不错");
            break;
        case 'C':
            out.print("差");
            break;
    }
%>
```

### 2.3 for 循环：

```java
<%for (int fontSize = 1;fontSize <=3;fontSize++) {%>
    <div style="color:#7fffd4;font-size: <%= fontSize%>0px">HELLO</div>
<% } %>
```

运行效果为：

![](https://static.pushy.site/files/jsp_for_effect.png)


### 2.4 while 循环：

同样可以用`for`循环来完成上面的例子：

```java
<%
    int fontSize = 1;
    <%while ( fontSize <= 3){ %>
    <div style="color:green;size=<%= fontSize %>">
        HELLO
    </div>
    <%fontSize++;%>
<%}%>
```