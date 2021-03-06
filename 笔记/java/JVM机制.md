## 类加载机制

* 加载：将class文件字节码内容加载到内存中，并将这些静态数据转换成方法区的运行时数据结构，然后生成一个代表这个类的java.lang.Class对象

* 链接：将Java类的二进制代码合并到JVM的运行状态之中的过程
    + 验证：确保加载的类信息符合JVM规范，没有安全方面的问题(报错在这步)
    + 准备：正式为类变量(static)分配内存并设置类变量默认初始值的阶段，这些内存都将在方法区中进行分配
    + 解析：虚拟机常量池内的符号引用(常量名)替换为直接引用(地址)的过程
    
    ~~~java
    class A{
        // n1是实例属性，不是静态变量，所以在链接阶段不处理
        public int n1 = 10;
        // n2是静态变量，在链接的准备阶段分配内存，值为 0
        public static int n2 = 20;
        // n3是static final，所以直接赋值 n3 = 30
        prblic static final int n3 = 30;
    }
    ~~~
    
* 初始化：
    + 执行类构造器<clinit>()方法的过程。类构造器<clinit>()方法是由编译期自动收集类中所有类变量的赋值动作和静态代码块中的语句合并产生的(类构造器是构造类信息的，不是构造该类对象的构造器)
    +  当初始化一个类的时候，如果发现其父类还没有进行初始化，则需要先触发其父类的初始化。
    + 虚拟机会保证一个类的<clinit>()方法在多线程环境中被正确加锁和同步
    + 类的初始化只会有一次，当第二次创建这个类的对象时，不会在进行初始化

#### 什么时候会发生类初始化

* 类的主动引用(**一定会发生类的初始化**)
    * 当虚拟机启动，先初始化main方法所在的类
    * new一个类的对象
    * 调用类的静态成员(除了final常亮)和静态方法
    * 使用java.lang.reflect包的方法对类进行反射调用
    * 当初始化一个类，如果其父类没有被初始化，则会先初始化它的父类
* 类的被动引用(**不会发生类的初始化**)
    * 当访问一个静态域时，只有真正声明这个域的类才会被初始化。如：当通过子类引用父类的静态变量，不会导致子类初始化
    * 通过数组定义类引用，不会触发类的初始化
    * 引用常量不会触发此类的初始化(常量在链接阶段就存入调用类的常量池中了)

## 类加载的代理模式

java.lang.ClassLoader

* 代理模式
    * 交给其他加载器来加载指定的类   

#### 类加载器的层次结构(树状结构)

* 引导类加载器(辈分最高，C语言实现)（Bootstrap ClassLoader）
    * 用来加载Java的核心库，用原生代码来实现，并不是继承于java.lang.ClassLoader
* 扩展类加载器（Extensions ClassLoader）
    * 用来加载java的扩展库
* 应用程序类加载器（Application ClassLoader）
    * 根据java应用的类路径，一般来说，java应用的类都是它来完成加载
* 自定义类加载器
    * 开发人员可以通过继承 java.lang.ClassLoader类的方式实现自己的类加载器，以满足一些特殊的需求

#### 双亲委派机制

>  大白话：找到最高辈分的加载器，然后层层往下。加载器发现有这个类，就不会再去加载自己写的类。为了保护Java核心库。如果一路向下没找到就会去加载自己写的类

* 某个特定的类加载器在接到加载类的请求时，首先将加载任务委托給父类加载器，一次追溯，知道最高的爷爷辈的，如果父类加载器可以完成类加载任务，就成功返回；只有父类加载器无法完成此加载任务时才会自己加载。

* 双亲委派机制是为了保证Java核心库的类型安全(也就是不会出现用户自己定义Java.lang.Object类的情况)
* 双亲委派机制是代理模式的一种
    * 并不是所有的类加载器都采用双亲委派机制
    * tomcat服务器类加载器也使用代理模式，不同的是它是首先尝试去加载某个类，如果找不到在代理给父类加载器，这与一般类加载器的顺序相反

双亲委派机制的问题：如果在辈分高一点的加载器中找到，就不会降级去查找一些类。会出现明明在Application ClassLoader中可以找到的，却找不到了

#### 线程上下文类加载器

线程类加载器是为了规避双亲委派机制的一些问题

每个线程都有一个关联的上下文类加载器

~~~java
Thread.currentThread().getContextClassLoader();//获取当前线程的类加载器
Thread.currentThread().getContextClassLoader();//设置当前线程的类加载器
~~~



