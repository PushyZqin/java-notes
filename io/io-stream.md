## 1. Stream（流）：

stream（流），是一种抽象的数据结构，一个流可以理解为一个数据的序列。输入流表示从一个源读取数据，输出流表示向一个目标写数据。例如敲打键盘的字符流从键盘输入到应用程序，对应着标准输入流（stdin）；应用程序将字符一个一个输出到显示器上，对应着标准输出流（stdout）。

简单来说，流的特点就是读一点数据，处理一点点数据。


## 2. IO流：

这里所说的输入输出流区别于JAVA8新特性中的Stream，按照输入输出流的使用来分类的话，有字节流和字符流两种（图片只罗列出部分的IO流）：

![](https://static.pushy.site/files/32423423423423.png)

首先，字符流的底层就是字节流。字节流读取单位为`byte`作为储存基本单位的文件，可以用来**读取很多格式的文件**，例如图片视频等。而字符流呢，主要是用来**读取文件的文本内容的**。

从上图可以看出，简单来看，以`Stream`结尾的都是字节流，以`Reader`或`Write`结尾的都是字符流。下面我们开始介绍这两种`IO`流
