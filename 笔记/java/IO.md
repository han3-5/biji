## 文件

>  文件在程序中是以流的形式来操作的

流：数据在数据源（文件）和程序（内存）之间经历的路径

输入流：数据从 **数据源（文件）** 到 **程序（内存）**的路径

输出流：数据从 **程序（内存）** 到 **数据源（文件）** 的路径

**创建文件**

> windows 的文件是" D:\\"
>
> linux 是" /"

~~~java
File 变量名 = new File(path)
File.separator 				 // 分隔符，自动匹配系统
"D:"+File.separator+"路径"	// 相当于 D:\路径
~~~

#### 字符字节区别

>  字符都**可以**转成字节，字节**不一定可以**转成字符
>
> 因为字节还有图像，音频等

字节流：按照**字节**读取数据，二进制文件		InputStream/OutputStream

字符流：按照**字符**读取数据，文本文件			Reader/Writer

原理：字符流的底层还是基于字节流操作，自动搜寻了指定的码表

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
delete()//删除文件，如果删除目录，只能
mkdir()//创建目录,需要保证父目录存在，不然会失败
mkdirs()//创建目录，如果父目录不存在，会将父目录一起创建
    
list()//下级名称返回是一个String类型数组，只是名字
    String[] list = 路径.list();
listFiles()//下级File对象，返回一个File类的引用，具有类的所有属性和方法
    File[] list = 路径.listFiles();
listRoots()//根路径（会输出所有盘符）
~~~

## 节点流和处理流

1. 节点流可以从一个特定的数据源 读写数据，如 FileReader、FileWriter
2. 处理流（也叫 包装流）是"连接"在已存在的流（节点流或处理流）之上，为程序提供更加强大的读写功能，如 BufferedReader、BufferedWriter

**区别**

1. 节点流是底层流，直接和数据源相接
2. 处理流包装节点流，既可以消除不同节点流的实现差异，也可以提供更方便的方法来完成输入输出
3. 处理流 对节点流进行包装，使用了 修饰器 设计模式，不会与数据源直接相连

处理流的功能主要体现在：

1. 性能的提高：主要增加缓冲的方式来提高输入输出的效率
2. 操作的便捷：处理流可能提供了一系列便捷的方法来一次输入输出大量的数据，使其更加灵活方便

## 序列化和反序列化

序列化：就是在保存数据时，保存数据的值和数据类型

反序列化：就是在恢复数据时，恢复数据的值和数据类型

需要让某个对象支持序列化机制，则必须让其类是可序列化的，即实现两个接口之一：

- Serializable		// 推荐使用这个，是一个标记接口
- Externalizable    // 该接口有方法需要实现 

**注意点：**

1. 反序列化的顺序需要和保存数据的顺序一致

2. 要求实现序列化或反序列化对象，需要 实现 Serializable

3. 序列化的类中建议添加 SerialVersionUID（序列化版本号），当类新添加了一个属性时，JVM 就可以不当成一个全新的类了

    ~~~java
    private static final long serialVersionUID = 1L;
    ~~~

4. 序列化对象时，默认将里面所有属性都进行序列化，但不包括 **static** 和 **transient** 修饰的成员

5. 序列化对象时，要求里面属性的**类型**也需要实现序列化接口

6. 序列化具备可继承性，也就是如果某类已经实现了序列化，其所有子类默认也实现了序列化

7. 如果序列化一个对象，序列化和反序列化的程序都可以看到其对象的类才可以

## InputStream

是一个抽象类

~~~java
public abstract class InputStream implements Closeable{}
~~~

#### FileInputStream

> 文件输入流

~~~java
// 构造方法
new FileInputStream(File file);
new FileInputStream(String path);
new FileInputStream(FileDescriptor fdObj);
~~~

~~~java
// 读取文件
FileInputStream fileInputStream = null;
try {
    fileInputStream = new FileInputStream("../hjstudy/src/test.txt");
    int len = 0;
    byte[] buffer = new byte[1024];
    while ((len = fileInputStream.read(buffer)) != -1){
        System.out.print(new String(buffer,0, len));
    }
} catch (IOException e) {
    e.printStackTrace();
}finally {
    try {
        fileInputStream.close();
    } catch (IOException e) {
        e.printStackTrace();
    }
}
~~~

#### BufferedInputStream

> 缓冲字节输入流

~~~java
// 构造方法
new BufferedInputStream(InputStream in);
new BufferedInputStream(InputStream in, int size);
~~~



#### ObjectInputStream

> 对象字节输入流 . 提供反序列化
>
> 反序列化的顺序需要和保存数据的顺序一致，不然会异常

~~~java
// 构造方法
new ObjectInputStream(InputStream in);
new ObjectInputStream();
~~~

~~~java
String path = "../hjstudy/src/testObject";
ObjectInputStream objectInputStream = new ObjectInputStream(new FileInputStream(path));
System.out.println(objectInputStream.readInt());
System.out.println(objectInputStream.readBoolean());
System.out.println(objectInputStream.readUTF());
System.out.println(objectInputStream.readObject());
Dog dog = (Dog) objectInputStream.readObject();	// 在ObjectOutputStream 处
System.out.println(dog.getName());
objectInputStream.close();
~~~

## OutputStream

是一个抽象类

~~~java
public abstract class OutputStream implements Closeable, Flushable{}
~~~

#### FileOutputStream

> 文件输出流

~~~java
// 构造方法
new FileOutputStream(File file);	// 默认append = false
new FileOutputStream(File file,boolean append);
new FileOutputStream(String path);
new FileOutputStream(String path,boolean append);
new FileOutputStream(FileDescriptor fdObj);
~~~

~~~java
FileOutputStream fileOutputStream = null;
String path = "../hjstudy/src/test1.txt";
try {
   // fileOutputStream = new FileOutputStream(path,true); // 追加
    fileOutputStream = new FileOutputStream(path);	// 覆盖
    fileOutputStream.write("nihao张".getBytes(StandardCharsets.UTF_8));
} catch (IOException e) {
    e.printStackTrace();
}finally {
    try {
        fileOutputStream.close();
    } catch (IOException e) {
        e.printStackTrace();
    }
}
~~~

#### BufferedOutputStream

~~~java
// 构造器
new BufferedOutputStream(OutputStream out);
new BufferedOutputStream(OutputStream out, int size);
~~~



#### ObjectOutputStream

> 提供序列化

~~~java
// 构造方法
new ObjectOutputStream(OutputStream out);
new ObjectOutputStream();
~~~

~~~java
public class ObjectOutputStreamTest {
    public static void main(String[] args) throws IOException {
        String path = "../hjstudy/src/testObject";
        ObjectOutputStream objectOutputStream = new ObjectOutputStream(new FileOutputStream(path));
        // 序列化数据
        objectOutputStream.writeInt(100); // 100 -> Integer(Integer实现过序列化了)
        objectOutputStream.writeBoolean(true);// boolean
        objectOutputStream.writeUTF("你好啊"); // String
        objectOutputStream.writeObject(new Dog("wa",10));// 保存一个对象
        objectOutputStream.close(); // 关闭
    }
}
class Dog implements Serializable{
    private String name;
    private int age;
    public Dog(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
~~~

## Reader

是一个抽象类

~~~java
public abstract class Reader implements Readable, Closeable{}
~~~

#### FileReader

~~~java
public class FileReader extends InputStreamReader{}
// 构造方法
new FileReader(String fileName);
new FileReader(File file);
new FileReader(FileDescriptor fd)
~~~

#### BufferedReader

~~~java
// 构造方法
new BufferedReader(Reader in);
new BufferedReader(Reader in, int sz);
~~~

~~~java
String path = "../hjstudy/src/test1.txt";
BufferedReader bufferedReader = null;
try {
    bufferedReader = new BufferedReader(new FileReader(path));
    String strDate;
    while ((strDate = bufferedReader.readLine()) != null){
        System.out.println(strDate);
    }
} catch (IOException e) {
    e.printStackTrace();
}finally {
    try {
        // 关闭处理流就可以了，底层会去自动关闭所用的节点流
        bufferedReader.close();
    } catch (IOException e) {
        e.printStackTrace();
    }
}
~~~

## Writer

是一个抽象类

~~~java
public abstract class Writer implements Appendable, Closeable, Flushable{}
~~~

#### FileWriter

注：FileWriter 使用后，必须要 **关闭（close）**或 **刷新（flush）**否则写不到文件

~~~java
public class FileWriter extends OutputStreamWriter{}
// 构造方法
new FileWriter(String fileName);		// 默认append = false
new FileWriter(String fileName,boolean append);
new FileWriter(File file);
new FileWriter(File file,boolean append);
new FileWriter(FileDescriptor fd);
~~~

#### BufferedWriter

~~~java
// 构造方法
new BufferedWriter(Writer out);
new BufferedWriter(Writer out, int sz);
~~~

~~~java
// 默认是覆盖
new BufferedWriter(new FileWriter(path));		// 覆盖
new BufferedWriter(new FileWriter(path,true));	// 追加
bufferedWriter.newLine();	// 换行符 这个换行符是和系统相关的
~~~

## 标准输入输出

标准输入：System.in		InputStream			键盘

~~~java
// System 类的 public final static InputStream in = null;
System.in				// InputStream			编译类型
System.in.getClass();	// BufferedInputStream	运行类型
~~~

标准输出：System.out	PrintStream			 显示器

~~~java
// System 类的 public final static PrintStream out = null;
System.out				// PrintStream			编译类型
System.out.getClass();	// PrintStream			运行类型
~~~

## 转换流

转换流可以将字节流转换为字符流，而字节流可以指定编码方式

#### InputStreamReader

> 想使用字符流，但是需要解决中文乱码问题

~~~java
public class InputStreamReader extends Reader{}
// 构造方法
new InputStreamReader(InputStream in);
new InputStreamReader(InputStream in, Charset cs);
new InputStreamReader(InputStream in, String charsetName);
new InputStreamReader(InputStream in, CharsetDecoder dec);
~~~

~~~java
InputStreamReader inputStreamReader = new InputStreamReader(new FileInputStream(path), "UTF-8");
BufferedReader bufferedReader = new BufferedReader(inputStreamReader);

bufferedReader.close();	// 关闭资源
~~~

#### OutputStreamWriter

> 想使用字符流，但是需要解决中文乱码问题

~~~java
public class OutputStreamWriter extends Writer{}
// 构造方法
new OutputStreamWriter(OutputStream out);
new OutputStreamWriter(OutputStream out, Charset cs);
new OutputStreamWriter(OutputStream out, String charsetName);
new OutputStreamWriter(OutputStream out, CharsetEncoder enc);
~~~

~~~java
OutputStreamWriter outputStreamWriter = new OutputStreamWriter(new FileOutputStream(path),"UTF-8");
BufferedWriter bufferedWriter = new BufferedWriter(outputStreamWriter);

bufferedWriter.close();	// 关闭资源
~~~

## Properties

常见方法

~~~java
load()	// 加载配置文件的键值对到Properties对象
list()	// 将数据显示到指定设备
getProperty(key)		// 根据键获取值
setProperty(key,value)  // 设置键值对到Properties对象
store()	// 将Properties中的键值对存储到配置文件，如果有中文，默认会存储unicode码
~~~

~~~java
String path = "../hjstudy/src/jdbc.properties";
// 1. 创建Properties 对象
Properties properties = new Properties();
// 2. 加载指定配置文件
properties.load(new FileReader(path));
// 3. 将k-v 显示到控制台
properties.list(System.out);
// 4. 根据 key 获取对应的值
System.out.println(properties.getProperty("username"));

// 修改，不存在就创建，存在就覆盖
properties.setProperty("test","你好");
// comments 是注释，此处不要，写的 null
properties.store(new FileWriter(path),null);
~~~

## CommonsIO

> 需要下载安装

#### FIleUtils

> 封装了一些方法
