## JDBC

>  sun 公司为了简化开发人员的操作（对数据库的统一），提供了一个规范，俗称 JDBC。这些规范的实现由具体的厂商去做

## JDBC 程序

1.  导入数据库驱动
    1. 下载 jar包[MySQL :: Download MySQL Connector/J (Archived Versions)](https://downloads.mysql.com/archives/c-j/) operatiing system：选择plaltform independent(与平台无关的)
    2. 拉到项目目录的 **`lib`**目录下（lib 目录下存放外部第三方库）
    3. 右键 之后点击 **`add as library`**将外部 jar包添加到项目中
2.  固定写法
    1. 加载驱动
    2. 设置url、用户密码
    3. 连接数据库
    4. 创建一个执行sql的对象
    5. sql的对象去执行sql语句
    6. 释放资源

~~~java
// mysql8.0 需要设置时区 serverTimezone=
String url = "jdbc:mysql://localhost:3306/school?useUnicode=true&characterEncoding=utf8&useSSl=true";
//url = "jdbc:mysql://主机地址:端口/数据库?参数1&参数2···"
//useUnicode = true		是否使用Unicode字符集					   默认false
//characterEncoding = utf8 Unicode为true时，指定字符编码			
//useSSl = true 			安全连接							
//autoReconnect             当数据库连接异常中断时，是否自动重新连接？ 默认false
//autoReconnectForPools     是否使用针对数据库连接池的重连策略默		默认false
~~~

~~~java
// 1. 加载驱动
Class.forName("com.mysql.jdbc.Driver");	// 会自动完成注册驱动
// 2. 传入用户信息和url
String username = "root";
String password = "123456";
String url = ".........";
// 3. 连接数据库，会返回一个 Connection 对象
Connection connection = DriverManager.getConnection(url,username,password);
// 4. 执行sql的对象 Statement
Statement statement = connection.createStatement();
// 5. 执行sql的对象 去执行sql 可能产生结果 ResultSet
String sql = "";	//sql 语句
ResultSet resultSet = statement.executeQuery(sql);	//使用ResuleSet来接受结果集。executeQuery是查询语句
while(resultSet.next()){	//因为是个结果集，想读出来必须使用.next()来控制指针
    resultSet.getObject("数据库列名");
    resultSet.getObject("数据库列名");
                .
                .
                .

    resultSet.getObject("数据库列名");
}
// 6. 释放资源
resultSet.close();
statement.close();
connection.close();
~~~

~~~JAVA
// 3. 连接数据库，会返回一个 Connection 对象
Connection connection = DriverManager.getConnection(url,username,password);
//connection 就代表数据库
~~~

~~~java
// 4. 执行sql的对象 Statement
Statement statement = connection.createStatement();
statement.executeQuery(sql);	// 查询操作。			返回值是ResultSet对象
statement.executeUpdate(sql);	// 更新、插入、删除操作。  返回值是int(受影响的行数)
statement.execute(sql);			// 任何操作。			返回值是boolean
~~~

#### 封装

建立一个jdbc.properties 文件放置jdbc 配置文件

~~~java
// 
driver = com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/schooluseUnicode=true&characterEncoding=utf8&useSSl=true
username=root
password=123456
~~~

~~~java
private static String driver;
private static String url;
private static String username;
private static String password;
static {
    // 通过反射拿到 jdbc.properties 信息
    Properties properties = new Properties();
    properties.load(new FileInputStream("文件位置")); 
    driver = properties.getProperty("driver");
    url = properties.getProperty("url");
    username = properties.getProperty("username");
    password = properties.getProperty("password");
    
    Class.forName(driver);	// 驱动只用加载一次
}
// 获取连接
public static Connection getconnection(){
    return DriverManager.getConnection(url,username,password);
}
// 释放连接资源
public static void release(Connection conn,Statement st,ResultSet rs){
    if(rs != null){
        rs.close();
    }
    if(st != null){
        st.close();
    }
    if(conn != null){
        conn.close();
    }
}
~~~

~~~java
//上面封装了1. 加载驱动 2. 设置url、用户密码  3. 连接数据库 6. 释放资源
~~~

#### statement 对象（不用，存在sql注入问题）

jdbc中的 statement对象用于向数据库发送 sql语句，想完成对数据库的增删改查

**增**

~~~java
connection = FengZhuang.getConnection();	// 加载驱动
String sql = "insert into subject(subjectname,classhour,gradeid) values('jdbc',150,5);";
statement = connection.createStatement();	// 执行sql的对象
int i = statement.executeUpdate(sql);	    // 执行sql
~~~

**删**

~~~java
connection = FengZhuang.getConnection();	// 加载驱动
String sql = "delete from subject where subjectno = '21';";
statement = connection.createStatement();	// 执行sql的对象
int i = statement.executeUpdate(sql);		// 执行sql
~~~

**改**

~~~java
connection = FengZhuang.getConnection();	// 加载驱动
String sql = "update subject set subjectname='jdbc1' where subjectno = 19;";
statement = connection.createStatement();	// 执行sql的对象
int i = statement.executeUpdate(sql);		// 执行sql
~~~

**查**

~~~java
connection = FengZhuang.getConnection();	// 加载驱动
String sql = "select * from subject"; 
statement = connection.createStatement();	// 执行sql的对象
resultSet = statement.executeQuery(sql);	// 执行sql 会返回一个结果集
~~~

#### PreparedStatement 对象

PreparedStatement 可以防止 sql注入。效率更好

**增**

~~~java
connection = FengZhuang.getConnection();				// 加载驱动
// 使用PreparedStatement的区别	使用？占位符代替参数
String sql = "insert into subject(subjectname,classhour,gradeid) values(?,?,?);";
preparedStatement = connection.prepareStatement(sql);	// 预编译sql语句
preparedStatement.setString(1,"qwer");					// 手动給参数赋值	setxxx(位置.值)
preparedStatement.setInt(2,200);						// 手动給参数赋值	setxxx(位置.值)
preparedStatement.setInt(3,9);							// 手动給参数赋值	setxxx(位置.值)
int i = preparedStatement.executeUpdate();				// 执行sql
~~~

**删**

~~~java
connection = FengZhuang.getConnection();				// 加载驱动
String sql = "delete from subject where subjectno = ?;";
preparedStatement = connection.prepareStatement(sql);	// 预编译sql 语句
preparedStatement.setInt(1,22);							// 手动給参数赋值	setxxx(位置.值)
int i = preparedStatement.executeUpdate();				// 执行sql
~~~

**改**

~~~java
connection = FengZhuang.getConnection();				// 加载驱动
String sql = "update subject set subjectname=? where subjectno = ?;";
preparedStatement = connection.prepareStatement(sql);	// 预编译sql 语句
preparedStatement.setString(1,"qwer1");					// 手动給参数赋值	setxxx(位置.值)
preparedStatement.setInt(2,23);							// 手动給参数赋值	setxxx(位置.值)
int i = preparedStatement.executeUpdate();				// 执行sql
~~~

**查**

~~~java
connection = FengZhuang.getConnection();				// 加载驱动
//String sql = "select * from subject where subjectno = ?";
String sql = "select * from subject";
preparedStatement = connection.prepareStatement(sql);	// 预编译sql
//preparedStatement.setInt(1,19);						// 手动給参数赋值	setxxx(位置.值)
resultSet = preparedStatement.executeQuery();   		// 执行sql 会返回一个结果集
~~~

## idea 连接数据库

设置选项中有一个**`Schemas`**来选择数据库

在idea数据库中操作数据库，需要提交才能更改成功

## 事务

1. 开启事务**`conn.setAutoCommit(false);`**
2. 一组业务执行完毕，提交事务
3. 可以在catch 语句中设置回滚语句(默认失败就会回滚)

~~~java
try{
    connection = FengZhuang.getConnection();			// 加载驱动
    connection.setAutoCommit(false);					// 关闭数据库自动提交，自动会开启事务
    String sql="";
    preparedStatement = connection.preparedStatement(sql);
    preparedStatement.executeUpdate();
    String sql2="";
    preparedStatement = connection.preparedStatement(sql2);
    preparedStatement.executeUpdate();
    connection.commit();		// 业务完毕，提交事务
}catch(){
    connection.rollback();		// 失败了则回退
}
~~~

## 批处理

> 可以使用java批处理，允许许多条语句一次性提交到数据库批量处理，减少编译次数，提高效率

~~~java
// JDBC的批量处理语句包含：
addBatch()	// 添加需要批量处理的SQl语句或参数
executeBatch()// 批量处理语句
clearBatch() // 清空批量处理语句
~~~

**JDBC连接mysql时，如果想使用批处理功能，需要在url中添加 `rewriteBatchedStatements=true`**

~~~java
// 传统
String sql = "insert into test values(?,?)";
PreparedStatement preparedStatement = connection.prepareStatement(sql);
for(int i = 0;i < 5000; i++){
    preparedStatement.setString(1,"tom" + i);
    preparedStatement.setString(2,"test");
    preparedStatement.executeUpdate();
}
~~~

~~~java
url=jdbc:mysql://localhost:3306/schooluseUnicode=true&characterEncoding=utf8&useSSl=true&rewriteBatchedStatements=true
// 批处理
String sql = "insert into test values(?,?)";
PreparedStatement preparedStatement = connection.prepareStatement(sql);
for(int i = 0;i < 5000; i++){
    preparedStatement.setString(1,"tom" + i);
    preparedStatement.setString(2,"test");
    // 将sql语句加入到批处理包中
    preparedStatement.addBatch();
    // 当有1000条记录时，批量执行
    if((i+1) % 1000 == 0){
        preparedStatement.executeBatch();
        preparedStatement.clearBatch(); // 清空
    }
}
~~~

## 连接池

**传统连接**：

1. 传统JDBC数据库连接使用DriverManager来获取，每次向数据库建立连接都要将Connection加载到内存中，在验证信息。需要连接的时候就向数据库请求一个，频繁的操作会占用很多的系统资源
2. 每一次数据库连接，使用完后都需要断开，如果程序出现异常而未能关闭，将导致数据库`内存泄露`
3. 传统连接的方式，不能控制创建连接的数量，如果连接过多，也可能出现内存泄漏或mysql崩溃

**数据库连接池**：

1. 预先在缓冲池中放入一定数量的连接，当需要建立数据库连接时，只需取出一个，使用完后放回
2. 数据库连接池负责分配、管理和释放数据库连接，它允许应用程序重复使用一个现有的数据库连接
3. 当应用程序向连接池请求的连接数超过最大连接数量时，这些请求会被加入到等待队列中

**JDBC的数据库连接池使用`javax.sql.DataSource`来表示** `DataSource`只是一个接口，通常由第三方实现

#### c3p0

>  需要先导入jar包

手动配置

~~~java
// 1.创建一个数据源对象
ComboPooledDataSoutce comboPooledDataSource = new ComboPooledDataSource();
// 2.获取连接信息
String driver = "com.mysql.cj.jdbc.Driver";
String url="jdbc:mysql://localhost:3306/schooluseUnicode=true&characterEncoding=utf8&useSSl=true";
String username="root";
String password="123456";
// 3.給数据源 comboPoolDataSource 设置相关的参数
comboPoolDataSource.setDriveClass(driver);
comboPoolDataSource.setJdbcUrl(url);
comboPoolDataSource.setUser(user);
comboPoolDataSource.setPassword(password);
comboPoolDataSource.setInitialPoolSize(10);// 设置初始化连接数
comboPoolDataSource.setMaxPoolSize(50);// 设置最大连接数

Connection connection = comboPoolDataSource.getConnection();// 建立连接
connection.close();
~~~

使用配置文件（常用）	**c3p0-config.xml**  放在src目录下，不要改名

~~~xml
<c3p0-config>
    <default-config>
        <property name="jdbcUrl">jdbc:mysql://localhost:3306/school&useUnicode=true&characterEncoding=utf8&useSSl=true</property>
        <property name="driverClass">com.mysql.cl.jdbc.Driver</property>
        <property name="user">root</property>
        <property name="password">123456</property>
        <!--每次增长的连接数-->
        <property name="acquireIncrement">3</property>
        <!--初始的连接数-->
        <property name="initialPoolSize">2</property>
        <!--最小连接数-->
        <property name="minPoolSize">5</property>
        <!--最大连接数-->
        <property name="maxPoolSize">50</property>
    </default-config>
    <name-config name="test12">
        也可以将 default-config换成带名字的，在连接的ComboPooledDataSource("test12");加上名字就可以
        <property name="driverClass">com.mysql.cl.jdbc.Driver</property>
        <property name="user">root</property>
        <property name="password">123456</property>
        ......
    </name-config>
</c3p0-config>
~~~

~~~java
ComboPooledDataSoutce comboPooledDataSource = new ComboPooledDataSource("test12");
Connection connection = comboPoolDataSource.getConnection();// 建立连接
connection.close();
~~~

#### Druid(德鲁伊)

> 需要先导入jar包

druid.properties

~~~properties
#key=value
#驱动
driverClassName=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/schooluseUnicode=true&characterEncoding=utf8&useSSl=true
#要连接的数据库的用户名和密码
username=root
password=123456
#initial connection Size    初始连接个数
initialSize=10
#min idle connecton size    空闲时期的最小连接数
minIdle=5
#max active connection size 最大连接数
maxActive=50
#max wait time (5000 mil seconds)   进程的最大等待时间
maxWait=5000
~~~

~~~java
Properties properties = new Properties();
properties.load(new FileInputStream("文件位置/druid.properties")); 
// 创建一个指定参数的数据库连接池
DataSource dataSource = DruidDataSourceFactory.createDataSource(properties);
Connection connection = dataSource.getConnection();// 建立连接
connection.close();
~~~

