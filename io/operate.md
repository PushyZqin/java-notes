## 1. 获取文件信息：

对文件的操作都是基于`File`对象，我们可以创建一个`File`对象来声明一个文件或者目录：

```java
import java.io.File;

File f = new File("E:\\demo.txt");  // 声明一个文件
File r = new File("E:\\demo")  // 声明一个目录
```

通过`File`我们有如下的方法来获得文件的信息：

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

- 通过`length()`获取文件的大小：

- 使用`lastModified()`和`setLastModified()`方法来获取和设置文件的最后修改日期和时间


## 2. 操作文件：

### 2.1 重构文件：

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

### 2.1 文件遍历：

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