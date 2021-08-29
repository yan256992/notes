## ⚡️  Java IO学习笔记

> IO也就是指输入与输出，指的是以内存为中心，对数据进行输入输出，由于代码最终是运行在内存中的，所以要读取数据的时候也需要将其加载到内存中才能操作，IO流是一种顺序读写数据的模式，它的特点是单向流动，数据类似自来水一样在水管中流动，所以将其称之为IO流

- **InputStream/OutputStream**

IO流以`byte`（字节）为最小单位，因此也称为字节流。字节的读取是按照顺序的，同样输出的时候也是按照顺序输出的。

<img src="C:/Users/25699/AppData/Roaming/Typora/typora-user-images/image-20210829110224729.png" alt="image-20210829110224729" style="zoom: 80%;" /><img src="https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210829110250602.png" alt="image-20210829110250602" style="zoom: 80%;" />

在Java中，`InputStream`代表输入字节流，`OuputStream`代表输出字节流，这是最基本的两种IO流。

- **Reader/Writer**

如果需要读取的是字符，并且字符不全是单字节表示的ASCII字符，那么，按照char来读写显然更加方便，这种就称为`字符流`

Java提供了`Reader`和`Writer`表示字符流，字符流传输的最小数据单位是`char`。

例如，我们把`char[]`数组`Hi你好`这4个字符用`Writer`字符流写入文件，并且使用UTF-8编码，得到的最终文件内容是8个字节，英文字符`H`和`i`各占一个字节，中文字符`你好`各占3个字节



:tada:`Reader`和`Writer`本质上是一个能自动编解码的`InputStream`和`OutputStream`。

- **同步和异步**

同步：读写IO时必须等待数据返回之后才可以继续执行后续代码，CPU利用率很低

异步：读写IO时仅发出请求，然后立刻执行后续代码，优点是CPU利用率高，但是代码编写复杂

上述的都是同步IO

### File对象

Java的标准库`java.io`提供了`File`对象来操作文件和目录

构造一个File对象的时候需要传入这个文件的路径

```Java
File f = new File("C:\\Windows\\notepad.exe");
```

传入的路径可以是绝对路径 也可以是相对路径

```Java
File f1 = new File("sub\\javac"); // 绝对路径是C:\Docs\sub\javac
File f3 = new File(".\\sub\\javac"); // 绝对路径是C:\Docs\sub\javac
File f3 = new File("..\\sub\\javac"); // 绝对路径是C:\sub\javac
```

用`File`对象获取到一个文件时，还可以进一步判断文件的权限和大小：

可以用`.`表示当前目录，`..`表示上级目录。

- `boolean canRead()`：是否可读；
- `boolean canWrite()`：是否可写；
- `boolean canExecute()`：是否可执行；
- `long length()`：文件字节大小。

和文件操作类似，File对象如果表示一个目录，可以通过以下方法创建和删除目录：

- `boolean mkdir()`：创建当前File对象表示的目录；
- `boolean mkdirs()`：创建当前File对象表示的目录，并在必要时将不存在的父目录也创建出来；
- `boolean delete()`：删除当前File对象表示的目录，当前目录必须为空才能删除成功。

### InputStream

InputStream是一个抽象类，是所有输入流的超类，这个抽象类定义的一个最重要的方法就是`int read()`,这个方法会读取输入流的下一个字节，并返回字节的int值。如果已经读取到末尾，就返回-1，表示不能继续读取了

FileInputStream是其的一个子类，也就是指从文件中读取数据

```Java
public void readFile() throws IOException {
    InputStream input = null;
    try {
        input = new FileInputStream("src/readme.txt");
        int n;
        while ((n = input.read()) != -1) { // 利用while同时读取并判断
            System.out.println(n);
        }
    } finally {
        if (input != null) { input.close(); }
    }
}
```

因为在读取数据的时候可能会发生IO异常 导致此时的流不能正确的关闭，所以在这采用`try-catch-finally`。无论是否发生错误最后都会关闭资源

#### 缓冲

在读取流的时候，一次读取一个字节并不是最高效的方法，因为会导致多次的对操作系统的IO请求，因此可以采用缓冲区，来提高文件读取的效率。InputStream提供了两个重要的重载方法来支持读取多个字节

<img src="https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210829155922962.png" alt="image-20210829155922962" style="zoom:50%;" />

- `int read(byte[] b)`：读取若干字节并填充到`byte[]`数组，返回读取的字节数
- `int read(byte[] b, int off, int len)`：指定`byte[]`数组的偏移量和最大填充数

利用上述方法一次读取多个字节时，需要先定义一个`byte[]`数组作为缓冲区，`read()`方法会尽可能多地读取字节到缓冲区， 但不会超过缓冲区的大小。`read()`方法的返回值不再是字节的`int`值，而是返回实际读取了多少个字节。如果返回`-1`，表示没有更多的数据了。

```java
FileInputStream in = new FileInputStream(f);
//输入缓冲流 套到Inputstream中
BufferedInputStream bis = new BufferedInputStream(in);
byte[] b = new byte[1024];
int len = bis.read(b);
while(len!=-1){ 
    //write
}
```

#### 阻塞

在调用InputStream的`read`方法的时候。read是阻塞的，这指的是。必须等待read执行完成之后，后面的代码才可以继续执行

### OutputStream

和输入流类似，OutputStream是抽象类，它是所有的输出流的超类，这个抽象类的一个重要的方法就是`write(int b)`

 :circus_tent: 该方法会写入一个字节到输出流，虽然传入的是int四个字节，但是实际上只会传入低八位表示字节的部分

`OutputStream`还提供了一个`flush()`方法，它的目的是将缓冲区的内容真正输出到目的地。

通常情况下，我们不需要调用这个`flush()`方法，因为缓冲区写满了`OutputStream`会自动调用它，并且，在调用`close()`方法关闭`OutputStream`之前，也会自动调用`flush()`方法。例如发送信息的时候 不需要等到缓冲区满才发送 那么就可以调用这个方法

:cactus:实际上，`InputStream`也有缓冲区。例如，从`FileInputStream`读取一个字节时，操作系统往往会一次性读取若干字节到缓冲区，并维==护一个指针指向未读的缓冲区==。然后，每次我们调用`int read()`读取下一个字节时，可以直接返回缓冲区的下一个字节，避免每次读一个字节都导致IO操作。当缓冲区全部读完后继续调用`read()`，则会触发操作系统的下一次读取并再次填满缓冲区。

```java
public void writeFile() throws IOException {
    try (OutputStream output = new FileOutputStream("out/readme.txt")) {
        output.write("Hello".getBytes("UTF-8")); // Hello
    } // 编译器在此自动为我们写入finally并调用close()
}
```

### 序列化

序列化就是值将一个Java对象转换成二进制内容存储，本质上就是一个`byte`数组

:question: 为什么需要对对象进行序列化？

因为序列化后可以把`byte`保存到文件中，或者把数组通过网络传到远程，这样就可以把对象存储的文件通过网络传递出去了。

序列化必须实现一个接口`java.io.Serializable`

`Serializable`接口没有定义任何方法，它是一个空接口。我们把这样的空接口称为“标记接口”（Marker Interface），实现了标记接口的类仅仅是给自身贴了个“标记”，并没有增加任何方法。

```Java
ByteArrayOutputStream buffer =  new ByteArrayOutputStream();
try(ObjectOutputStream otp = new ObjectOutputStream(buffer)){
    otp.writeInt(12345);
    otp.writeObject(Double.valueOf(122.00));
}
System.out.println(Arrays.toString(buffer.toByteArray()));
}
```

### Reader

`Reader`是一个输入流，与inputStream不同的是一个是字节流一个是字符流，Reader是以`char`为单位进行读取数据的、是所有的字符输入流的超类

| InputStream                         | Reader                                |
| :---------------------------------- | :------------------------------------ |
| 字节流，以`byte`为单位              | 字符流，以`char`为单位                |
| 读取字节（-1，0~255）：`int read()` | 读取字符（-1，0~65535）：`int read()` |
| 读到字节数组：`int read(byte[] b)`  | 读到字符数组：`int read(char[] c)`    |

在读取文件的时候如果有中文 那么需要在创建FileReader的时候就指定编码

```Java
Reader reader = new FileReader("src/readme.txt", StandardCharsets.UTF_8);
```

和InputStream类似，Reader也提供了缓冲的功能，一次性读取若干的字符到char数组中，返回实际读入的字符个数，如果返回-1发表没有数据了

```java
public void readFile() throws IOException {
    try (Reader reader = new FileReader("src/readme.txt", StandardCharsets.UTF_8)) {
        char[] buffer = new char[1000];
        int n;
        while ((n = rea3der.read(buffer)) != -1) {
            Sysem.out.println("read " + n + " chars.");
        }
    }
}
```

### Writer

`Reader`是带编码转换器的`InputStream`，它把`byte`转换为`char`，而`Writer`就是带编码转换器的`OutputStream`，它把`char`转换为`byte`并输出。

`Writer`和`OutputStream`的区别如下：

| OutputStream                           | Writer                                   |
| :------------------------------------- | :--------------------------------------- |
| 字节流，以`byte`为单位                 | 字符流，以`char`为单位                   |
| 写入字节（0~255）：`void write(int b)` | 写入字符（0~65535）：`void write(int c)` |
| 写入字节数组：`void write(byte[] b)`   | 写入字符数组：`void write(char[] c)`     |
| 无对应方法                             | 写入String：`void write(String s)`       |

`Writer`是所有字符输出流的超类，它提供的方法主要有：

- 写入一个字符（0~65535）：`void write(int c)`；
- 写入字符数组的所有字符：`void write(char[] c)`；
- 写入String表示的所有字符：`void write(String s)`。

### 转换流

![image-20210829164923418](https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210829164923418.png)

`InputStrreamReader`，`OutputStreamWriter`就是将字节输入流转换为字符输入流，将字节输出流转换为字符输出流

```Java
public static void main(String[] args) throws IOException {
    //获取文件对象
    File f = new File("D:\\test.txt");
    File fc = new File("D:\\testCopy.txt");
    //定义字节流接触文件
    FileInputStream inputStream = new FileInputStream(f);
    //定义转换流将文件从字节流转换为字符流 这里要指定套在哪个管上面 同时需要指定编码格式
    InputStreamReader ir = new InputStreamReader(inputStream,"utf-8");

    FileOutputStream otp = new FileOutputStream(fc);

    OutputStreamWriter ow = new OutputStreamWriter(otp,"utf-8");

    //由于已经转换为字符流 所以可以读取到字符数组中
    char[] des = new char[20];
    int len  = ir.read(des);
    while(len!=-1){
        ow.write(des,0,len); //写入
        System.out.println(new String(des,0,len));
        len = ir.read(des);
    }
    ow.close();
    ir.close();
}
```

