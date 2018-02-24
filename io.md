### 1. Stream（流）：

#### 1.1 流的含义：

`stream`（流），是一种抽象的数据结构，一个流可以理解为一个数据的序列。输入流表示从一个源读取数据，输出流表示向一个目标写数据。例如敲打键盘的字符流从键盘输入到应用程序，对应着标准输入流（stdin）；应用程序将字符一个一个输出到显示器上，对应着标准输出流（stdout）。

而且，流的特点是读一点数据，处理一点点数据。

#### 1.2 控制台读取输入：

为了获得一个绑定到控制台的字符串流，我们可以把`System.in`包装到一个`BufferedReader`对象来创建一个字符流：

```java
BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
```

##### 读取字符：

从`BufferedReader`对象中读取字符使用`read()`方法，每次调用该方法，它从输入流读取一个字符并把该字符作为整数值返回。当流结束时候返回`-1`，该方法抛出`IOException`：

```java
import java.io.*;

public class BRRead {
	// 必须要使用theows 来声明异常：
    public static void main(String[] args) throws IOException{
        char c;
        BufferedReader br = new BufferedReader(new InputStreamReader((System.in)));
        System.out.println("输入字符，按‘q’键退出。");
        do {
            c = (char) br.read();
            System.out.println(c);
        } while (c != 'q');
    }
}
```

#### 读取字符串：

```java
import java.io.*;

public class BRReadLines {
    public static void main(String[] args)throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String str;
        System.out.println("Enter 'exit' to quit");
        do {
            // 使用readLine()来读取一个字符串：
            str = br.readLine();
            System.out.println(str);
        } while (!str.equals("exit"));
    }
}
```

### 2.读写文件：

#### 2.1 文件操作：

对文件的操作都是基于`File`对象，我们可以创建一个`File`对象来声明一个文件或者目录：

```java
import java.io.File;

File f = new File("E:\\demo.txt");  // 声明一个文件
File r = new File("E:\\demo")  // 声明一个目录
```

##### 文件状态：

- 通过`exists()`来判断声明的`File`对象的路径名是否存在：

```java
File f = new File("E:\\demo.txt");
boolean fileExists = f.exists();
if (boolean) {
	// file exists...
} else {
	// file does not exists...
}
```

- 可以通过`isFile()`和`isDirectory()`方法来判断`File`对象是否是文件还是目录

```java
File f = new File("E:\\demo.txt");
boolean isFile = f.isFile();  // 返回true
boolean isDir = f.isDirectory();  // 返回false
```

#### 文件信息：

- 通过`length()`获取文件的大小：

- 使用`lastModified()`和`setLastModified()`方法来获取和设置文件的最后修改日期和时间

#### 文件操作：

- 创建文件：使用`createNewFile()`来创建`File`对象文件：

```java
File dummyFile = new File(filename);
// 创建成功返回True，否则返回False：
boolean fileCreated  = dummyFile.createNewFile();
```

- 创建文件夹：使用`mkdir()`创建目录：

```java
File newDir = new File("test");
newDir.mkdir();
```

- 删除文件： 使用`delete()`来删除`File`对象文件：

```java
File f = new File("good.html");
// 删除成功返回true，否则返回false：
boolean filedeleted = f.delete();
```

- 重命名文件：使用`renameTo()`方法来重命名，需要注意的是，新文件也需要声明一个`File`对象：

```java
File oldFile = new File(oldName);
// 用File对象来表示新文件：
File newFile = new File(newName);
boolean fileRenamed = oldFile.renameTo(newFile);
```

#### 文件遍历：

使用File类的`listRoots()`静态方法获取文件系统中可用根目录的列表。它返回一个**File对象数组**：

```java
private static void listFileDir() {
    File dir = new File("E:\\");
    File [] roots = dir.listFiles();
    for (File f :roots) {
        if (f.isFile()) {
            System.out.println("【file】" + f.getPath());
        } else {
            System.out.println("【dir】" + f.getPath());
        }
    }
}
```

还可以创建一个过滤器，过滤出获取的文件和目录：

```java
public class fileFilter {
    public static void main(String[] args) {
        String dirPath = "E:\\";
        File dir = new File(dirPath);
        // 创建一个文件过滤器，用来过滤出.txt文件：
        FileFilter filter = file -> {
            if (file.isFile()) {
                String fileName = file.getName().toLowerCase();
                // 如果文件名以.txt结尾则返回true：
                if (fileName.endsWith(".txt")) {
                    return true;
                }
            }
            return false;
        };
        File[] list = dir.listFiles(filter);  // 加载filter
        for (File f : list) {
            if (f.isFile()) {
                System.out.println("【file】" + f.getPath());
            } else {
                System.out.println("【dir】" + f.getPath());
            }
        }
    }
}
```

#### 2.2 FileInputStream：

#### 2.3 FileOutputStream：