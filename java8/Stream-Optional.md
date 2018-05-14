## 1. Stream


## 2. Optional

### 2.1 定义和基本方法

`Optional` 类是一个可以为`null`的容器对象。如果值存在则`isPresent()`方法会返回`true`，调用`get()`方法会返回该对象，该类能很好的解决空指针异常。

```java
public static void main(String[] args) {
    Integer value = 1;
    // Integer value = null;
    Optional<Integer> a = Optional.ofNullable(null);
    System.out.println("a是否存在 " + a.isPresent());
    // 如果a存在，则返回a，不存在则返回0
    System.out.println(a.orElse(new Integer(0)));
    // 如果a存在，则执行map中函数操作（自增1），不存在则返回0
    System.out.println(a.map(f -> f += 1).orElse(0));
}

// a是否存在 true
// 1
// 2

// a是否存在 false
// 0
// 0
```

`Optional`提供了一些基本的方法，例如：

- 存在则返回，无则提供指定的值：

```java
return object.orElse(null)
```

- 存在则返回，无则通过函数来产生：

```java
return object.orElseGet( () -> fetchRandomObject() )
```

- 存在则提供的映射方法（`lambda`表达式），无则返回一个空集合（`Optional.empty`）：

```java
return object.
		.map(obj -> obj.getPropertiy())
        .orElse(null)
```


### 2.2 应用

例如存在一个通过用户名查询用户名密码的`service`方法，并且返回查询用户的密码。需要调用`fetchUserByUserName`的`dao`方法，但是不能保证查询出的user对象是否为`null`，所以可以使用`Optional`类来避免出现空指针异常。

```java
@Overide
public String fetchPasswordByNameSerivce(String name) {
	User user = userMapper.fetchUserByUserName(name);
    return Optional.ofNullable(user)  // 允许从null中创建一个Optional对象
    	.map(user -> user.getUserPassword()) // 当user存在时，返回user对象的password
        .orElse("None") // 当user不存在时，在返回None
}
```

并且`map()`是无限极联的，例如假设一个用户只关联一个角色，那么在一个通过用户名查询到该用户的角色的角色名`service`方法就可以这样实现：

```java
@Overide
public String fetchRoleNameByNameSerivce(String name) {
	User user = userMapper.fetchUserByUserName(name);
    return Optional.ofNullable(user)  // 允许从null中创建一个Optional对象
    	.map(user -> user.getRole()) // 如果存在得到user关联的role对象
        .map(role -> role.getRoleName()) // 如果存在得到role的角色名
        .map(roleName -> roleName.toUpperCase()) // 将角色名转换成大写的形式
        .orElse("None")
}
```

<br>

>参考资料
>[【Java】jdk8 Optional 的正确姿势](https://blog.csdn.net/hj7jay/article/details/52459334)
