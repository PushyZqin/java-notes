## 1. 标准输入流：

标准输入流是从标准输入设备（键盘）流向程序的数据，JAVA的控制台输入由`System.in`完成。

首先，`System.in`是`InputStream`的一个实例，因此可以使用任何具体的装饰器从键盘读取数据，例如可以我们使用一个`BufferedReader`对象，创建一个缓冲功能的字符流：


```java
import java.io.*;

public class BufReaderTest {
    public static void main(String[] args) {
        try {
            BufferedReader bf = new BufferedReader(new InputStreamReader(System.in));
            int a;
            // 但没有到达流的结尾时（=1），循环读入字符：
            while ((a = bf.read()) != -1) {
                if ((char) a == 'q') break;  // 如果输入的字符为'q'则退出循环，不再读入
                System.out.print((char) a);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }

    }
}
```

我们也可以使用`readline()`方法从控制台中读取字符串：

```java
public class BRReadLines {
    public static void main(String[] args)throws IOException{
        String str;
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        System.out.println("Enter 'exit' to quit");
        do {
            // 使用readLine()来读取一个字符串：
            str = br.readLine();
            System.out.print(str);
        } while (!str.equals("exit"));
    }
}
```

## 2. 标准输出流：

标准输出流是把程序数据流向标准输出设备（屏幕）或控制台，JAVA的控制台输出由`System.out`完成，我们经常使用的`System.out.println("Hello World")`，就是标准输出流。

`print()`方法和`println()`方法都由类`PrintStream`定义，继承了父类`OutputStream`，并且实现了`write()`方法，所以使用`write()`方法也可以用来往控制台写操作。

```java
import java.io.*;

public class WriteTest {
   public static void main(String args[]) {
      int b;
      b = 'A';
      System.out.write(b);
   }
}
```
