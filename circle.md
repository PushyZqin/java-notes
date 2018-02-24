## 1.  while、do-while循环：

`while`循环：
```java
public class whileTest {
    public static void main(String[] args) {
        int i = 0;
        while (i < 10){
            System.out.println("The index is :" + i);
            i ++;
            System.out.println("\n");
        }
    }
}
```
`do-whle`循环，与`while`循环不同的是`do…while` 循环**至少会执行一次**：当第二次循环不满足`while`中的条件时，则不再进行循环：
```java
...
        int i = 0;
        do{
            System.out.println("The index is :" + i);
            i ++;
            System.out.println("\n");
        }while (i < 10);
```

## 2. for循环：

- 最简单的`for`循环：

```java
public class forTest {
    public static void main(String[] args) {
		for(int x =10;x <20;x++){
	    System.out.println("index of " + x);
    }
}
```
- 嵌套的`for`循环：
```java
public class forTest {
    public static void main(String[] args) {
		for(int i=1;i<=9;i++){
		    for(int j=1;j<=9;j++){
		        System.out.println(j + "x" + i + "=" + i * j);
		    }
    }
}
```
- 增强型循环：`foreach`循环：

例如对数组进行`foreach`循环操作，遍历数组的元素：

```java
public class forTest {
    public static void main(String[] args) {
    	//创建一个数组
		double[] myList = {1.9,2.9,3.4,3.6};
		for (double each:myList){
	    System.out.println(e);
		}
    }
}
```

## 3. 分支结构：

### 3. 1 if-else:

```java
public class branchStruc {
    public static void main(String[] args) {
    	//if-else 语句：
        if (x<20){
            System.out.println("声明的值小于20");
        }else {
            System.out.println("声明的值大于20");
        }
    }
}
```

### 3.2 if-else else-if:

```java
public class Hello {
    public static void main(String[] args) {
    	int y = 20;
        //if-else else-if语句：
        if (y == 20){
            System.out.println("y的值为20");
        }else if (y == 30){
            System.out.println("y的值为30");
        }
    }
}
```

### 3.3 switch-case:

`switch` 语句判断一个变量与一系列值中某个值是否相等，每个值称为一个分支

```java
public class Hello {
    public static void main(String[] args) {
    	char grade = 'C';
        switch (grade){
            case 'A':
                System.out.println("你的成绩优秀！！");
                break;
            case 'B':
                System.out.println("你的成绩良好！");
                break;
            case 'C':
                System.out.println("你的成绩一般.");
                break;
            case 'D':
                System.out.println("你的成绩较差");
                break;
        }
        System.out.println("你的成绩为：" +grade);
    }
}
```
