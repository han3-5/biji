## I/O

#### 字符字节区别

> 字符都**可以**转成字节，字节**不一定可以**转成字符
>
> 因为字节还有图像，音频等

> 字节流：按照**字节**读取数据
>
> 字符流：按照**字符**读取数据
>
> 原理：底层还是基于字节流操作，自动搜寻了指定的码表

>主要：
>
>File												 文件类
>
>InputStream								 字节输入流
>
>OutputStream							字节输出流
>
>Reader										  字符输入流
>
>Writer											字符输出流
>
>Closeable									  关闭流接口
>
>Flushable									  刷新流接口
>
>Serializable								   序列化接口 

## File

> windows 的文件是" D:\\"
>
> linux 是" /"

~~~java
File 变量名 = new File(path)
File.separator //分隔符，自动匹配系统
"D:"+File.separator+"路径"//相当于 D:\路径
~~~

#### 常用Api

~~~java
getName()//获取文件名
getPath()//获取路径名，使用的是绝对路径就返回绝对路径，相对路径就返回相对路径
getAbsolutePath()//获取绝对路径名
getParent()//获取父级路径
exists()//判断是否存在
isFile()//判断是否是文件
isDirectory()//判断是否是文件夹
length()//文件的长度	，如果是文件夹则返回 0
createNewFile()//创建新文件
delete()//删除文件
    
mkdir()//创建目录,需要保证父目录存在，不然会失败
mkdirs()//创建目录，如果父目录不存在，会将父目录一起创建
list()//下级名称返回是一个String类型数组，只是名字
    String[] list = 路径.list();
listFiles()//下级File对象，返回一个File类的引用，具有类的所有属性和方法
    File[] list = 路径.listFiles();
listRoots()//根路径（会输出所有盘符）
~~~

#### 标准步骤

 1. 创建源

     ~~~java
     File src = new File(path);
     ~~~

 

 2. 选择流

     ~~~java
     //以字节为例		读取，文件必须存在
     InputStream t = new FileInputStream(src);
     //写入	true代表追加 false代表覆盖	默认是false
     InputStream t1 = new FileOutputStream(src,true);
     ~~~

     ~~~java
     //字节数组流里面的close()方法什么都没有
     //因为在jvm中，可以不用通知系统关闭系统资源
     
     ByteArrayInputStream(字节数组)//输入到程序中，字节数组到文件
     
     ByteArrayOutputStream()//输出到字节数组，文件到字节数组
     ~~~

     ~~~java
     //加上缓冲流会有性能的提升，相当于用了更大的"卡车"
     BufferedInputStream//字节输入缓冲流
     BufferedOutputStream//字节输出缓冲流
     BufferedReader//字符输入缓冲流
     BufferedWriter//字符输出缓冲流
     ~~~

     > 字符的输出需要通过字符流来操作，但是本质最后还是通过字节流输出到计算机上进行存储的

     ###### 转换流

     ~~~java
     //转换流
     //从字节流到字符流的桥梁，读取字节，使用指定字符集解码为字符
     InputStreamReader isr = new InputStreamReader(字节流,"编码")
     //从字符流到字节流的桥梁，使用指定字符集编码为字节
     OutputStreamWriter osw = new OutputStreamWriter(字符流,"编码")
     ~~~

     ###### 数据流

     > 数据流
     >
     > 读取顺序与写入保持顺序一致。一般是先写入在读取

     ~~~java
     //从基础输入流读取原始java数据类型
     DateInputStream(InputStream in)
     //便携式方式将原始的java数据类型写入输出流
     DateOutputStream(OutputStream out)
     ~~~

     ~~~java
     DateOutputStream dos = new DateOutputStream(OutputStream out)
     dos.writeUTF("字符串");
     dos.writeInt(20);
     DateInputStream dis = new DateInputStream(InputStream in)
     //顺序与写出一致
     String str = dis.readUTF();
     int age = dis.readInt();
     ~~~

     ###### 对象流

     > 对象流		序列化与反序列化 	Serialization Deserialization 
     >
     > 对象流指直接**把一个对象以流的形式**传输给其他的介质
     >
     > **//TODO 不理解干嘛的**
     >
     > **序列化机制使得对象可以脱离程序的运行而独立存在**
     >
     > 只有支持java.io.Serialization 接口的对象才能写入流中
     >
     > 如果某个数据不想被序列化需要关键字 transient
     >
     > 写出	-->序列化
     >
     > 读取	-->反序列化

     ~~~java
     ObjectOutputStream oos = new ObjectOutputStream()
     ~~~

     ###### 随机读取和写入流RandomAccessFile

     > 分块思想

     ~~~java
     seek(字节)//从哪开始
     ~~~

     

 3. 操作 

     ~~~java
     ~~~

 



 4. 关闭，释放系统资源

     ~~~java
     //分别关闭，先打开的后关闭
     t1.close();
     
     t.close();
     //新版释放资源1.7之后
     try...with...resource
     try(需要释放的资源1;2;3){
         //代码
     }
     ~~~

#### 字符集

>US-ASCII	英文的ASCII
>
>ISO-8859-1	拉丁字符，包含中文、日文等
>
>UTF-8			变长unicode字符(1-3字节)，国际通用
>
>UTF-16BE	定长unicode字符(2字节)，大端，将高位的字节放在低地址表示
>
>UTF-16LE	定长unicode字符(2字节)，小端。将高位的字节放在高地址表示
>
>UTF-16

> 如果解码字符集不统一，会出现**乱码**

~~~java
//编码：字符串-->字节
//编码：编码成字节数组
String str = "你好a";
byte[] a = str.getBytes();

//解码：字节-->字符串
//解码：解码成指定字符集
String str;
			//	字节数组，从哪开始，长度，要解码成的字符集
str = new String(a,0,a.length, "utf-8"); 
~~~



#### 四大抽象类

> 字节：InputStream	OutputStream
>
> 常用方法(Input)：int read()	void close()
>
> ​				(Output): void write(int)	void flush()//刷新  void close()



> 字符：Reader	Writer
>
> 常用方法(R)：int read()    void close()
>
> ​				(W):void write(String)    void flush()      void close()

## CommonsIO

> 需要下载安装

#### FIleUtils

> 封装了一些方法
