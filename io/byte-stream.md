>在此之前，你可以先阅读下我写的文章[Java中InputStream装饰器模式的大家族](http://blog.pushy.site/posts/1519577691)，这篇文章以人物假设的形式来说明字节流中的装饰者的设计模式。

## 1. IO-Stream：

字节流中，`InputStream`和`OutputStream`为字节流操作的超类，包含了输入输出流读取数据和输入数据的基本方法，所有具体类都支持这些方法。

例如`FileInputStream`作为`InputStream`的子类，用于处理文件流，并且继承了父类的方法`read()`，该方法读取一个字节并将读取的字节作为`int`返回。当到达输入流的结尾时，它返回-1：

```java
import java.io.*;

public class fileInputStream{
    public static void main(String[] args) throws IOException{
        InputStream f = new FileInputStream("e:/text.txt");
        byte value;
        // 当没有达到输入流的结尾时，继续读取并打印转换成char类型的字符：
        while ((value = (byte) f.read()) != -1) {
            System.out.print((char) value);
        }
        f.close(); // 关闭输入流
    }
}
```

同样`ByteArrayInputStream`作为主要的输入流子类，用于处理字符数组，同样具有`read()`方法：

```java
public class ByteArrayStream {
    public static void main(String[] args) throws IOException{
        String str = "Hello World";
        byte[] b = str.getBytes(); // 创建字节数组
        ByteArrayInputStream bi = new ByteArrayInputStream(b);
        int result = -1;
        while ((result = bi.read()) != -1) {
            System.out.print((char) result);
        }
        bi.close();
    }
}
```

当然`FileOutStream`也是同样继承了`OutputStream`的`write()`方法：

```java
public class fileOutputStream {
    public static void main(String[] args) {
        String line2 = "Hello World";
        try {
            OutputStream fos = new FileOutputStream("E:\\inputTest.txt");
            // 使用getBytes()方法写入二进制数据：
            fos.write(line2.getBytes());
            fos.flush();  // 刷新输出流
            fos.close();  // 关闭输出流
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```


## 2. 装饰者模式：

从上图我们可以看到有个特殊的`FilterInputStream`，它包含了三个子类：`BufferedInputStream`、`PushbackInputStream`、`DataInputStream`。

在`InputStream`的所有子类当中采用了装饰者的设计模式，即**不改变原类文件和使用继承的前提下，动态地扩展一个对象的功能，通过创建一个包装对象，也就是装饰包裹真实的对象**。这三个类可以被认为就是提供动态扩展一个对象的功能的类，即所谓的“包装对象”，实现方式就是**将这个需要提供功能的对象放到这个类的构造函数中创建对象**。

例如在上面的`FileInputStream`处理字节流文件时，文件过大可能会导致读取时间过慢，因此可以给`FileInputStream`包装上`BufferedInputStream`的缓冲流功能来加快读取的速度：

```java
public class BufInputStream {
    public static void main(String[] args) throws Exception{
        try {
            // 通过缓冲区数据向输入流添加功能，维护一个内部缓冲区以存储从底层输入流读取的字节：
            BufferedInputStream bis  = new BufferedInputStream(new FileInputStream("E:\\text.txt"));
            byte byteData;
            while ((byteData = (byte) bis.read()) != -1) {
                System.out.print((char) byteData);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```