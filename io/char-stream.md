## 1. 读取/写入器：

处理字符流，主要基于超类`Reader`（读取器）和`Write`（写入器），当要从数据源读取基于字符数据时，使用`Reader`，当想要写入基于字符的数据时使用使用`Write`。

以`InputStreamReader`举例，我们可以从源码获知，`InputStreamReader`接受一个`InputStream`对象，和字符编码的名字`charsetName`

```java
// InputStreamReader.java源码文件部分代码

public InputStreamReader(InputStream in, String charsetName)
    throws UnsupportedEncodingException
    {
    	// other code...
    }
```

例如我们想从文件的数据源中读取基于字符的数据是，我们可以先创建一个`FileStream`对象，然后再在`InputStreamReader`去创建它：

```java
FileInputStream fis = new FileInputStream("E:\\text.txt");
// 在构造方法中创建fis对象，并设置字符集的编码格式为UTF-8：
InputStreamReader reader = new InputStreamReader(fis,"UTF-8");
```

这样我们就可以从`text.txt`文件中基于字符来读取文件中的数据了：

```java
import java.io.*;

public class InsReaderTest {
    public static void main(String[] args) throws Exception{
        FileInputStream fis = new FileInputStream("E:\\text.txt");
        // 设置读入字符集编码：
        InputStreamReader reader = new InputStreamReader(fis,"UTF-8");
        // 创建一个StringBuilder对象，该对象能够多次被修改：
        StringBuilder sb = new StringBuilder();
        // ready()中如果流是随时可以读取则返回true：
        while (reader.ready()) {
            sb.append((char) reader.read());
        }
        // 返回字符串的表现形式：
        System.out.println(sb.toString());
    }
}
```


## 2. 缓冲功能：

`Reader`/`Writer`有一个子类为：`BufferedReader`/`BufferedWriter`，从源码来看，该对象接受一个`Reader`/`Write`参数：

```java
// BufferedReader.java源码文件部分代码

public BufferedReader(Reader in) {
    this(in, defaultCharBufferSize);
}
```

这和上面的`InputStream`和`BufferedStream`的装饰者设计模式有异曲同工之处，我们可以在`BufferedReader`中的构造函数中来创建`InputStreamReader`对象，从而实现缓冲流的效果：


```java
import java.io.*;

public class BufReaderTest {
    public static void main(String[] args) throws Exception{
        FileInputStream fis = new FileInputStream("E:\\text.txt");
        BufferedReader br = new BufferedReader(new InputStreamReader(fis,"UTF-8"));
        String text = null;
        while ((text = br.readLine()) != null) {
            System.out.println(text);
        }
        br.close();
    }
}
```

同样，我们还可以用`BufferedWriter`包装`FileWriter`对象（为`InputStreamReader`的子类，同样属于`Writer`对象）：

```java
import java.io.*;

public class BufWriterTest {
    public static void main(String[] args) throws Exception{
        BufferedWriter bw = new BufferedWriter(new FileWriter("text.txt"))
        bw.append("Hello World");
        bw.newLine();
        bw.newLine();
        bw.append("你好！世界");
        // 刷新并清空缓冲区，将数据写入文件
        bw.flush();
		bw.close();
    }
}
```