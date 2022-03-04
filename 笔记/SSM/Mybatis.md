## 简介

- MyBatis 是一款优秀的**持久层框架**
- 它支持自定义 SQL、存储过程以及高级映射
- MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作
- MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO为数据库中的记录。

文档地址：[mybatis – MyBatis 3 ](https://mybatis.org/mybatis-3/zh/index.html)

## 获得Mybatis

- maven 仓库[ mybatis ](https://mvnrepository.com/artifact/org.mybatis/mybatis)
- Github[mybatis/mybatis-3](https://github.com/mybatis/mybatis-3)
- 中文文档[mybatis – MyBatis 3 ](https://mybatis.org/mybatis-3/zh/index.html)

## 第一个Mybatis程序

- 导入包

~~~xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.11</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.27</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.7</version>
</dependency>
~~~

- 编写mybatis的核心配置文件

> 在resources 目录下建一个mybatis-config.xml 配置文件

> 在设置url时，xml中的"&"需要换成" &amp ;"进行转义

~~~xml
<!--mysql8.0之后 url可能需要加上时区 serverTimezone=GMT-->
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--Mybatis核心配置文件-->
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/school?useUnicode=true&amp;characterEncoding=utf8&amp;useSSl=true"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
    <!--    每一个Mapper.xml都需要在mybatis核心配置文件中注册-->
    <mappers>
        <mapper resource="dao/UserMapper.xml"/>
    </mappers>
</configuration>
~~~

- 编写mybatis工具类

~~~java
public class MybatisUtils {
    private static SqlSessionFactory sqlSessionFactory;
    static {
        try {
            // 使用 Mybatis 第一步：获取sqlSessionFactory 对象
            String resource = "mybatis-config.xml";
            InputStream inputStream = Resources.getResourceAsStream(resource);
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    // 既然有了sqlSessionFactory，就可以从中获得 SqlSession 的实例了
    // SqlSession 完全包含了面向数据库执行 SQL命令所需的所有方法
    public static SqlSession getSqlSession(){
        return sqlSessionFactory.openSession();
    }
}
~~~

#### 编写代码

- 实体类

- Dao接口

    ~~~java
    public interface UserDao {
        public List<User> getUserList();
    }
    ~~~

- **接口实现类** 由原来的UserDaoImpl转变为一个Mapper配置文件.

    ~~~xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE mapper
            PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    
    <!--namespace=绑定的一个对应的Dao/Mapper接口-->
    <mapper namespace="dao.UserDao">
    	<!--select查询语句-->
        <!--id="实现的方法名" resultType="结果的类型"-->
        <select id="getUserList" resultType="pojo.User">
            select * from user
        </select>
    </mapper>
    ~~~

#### 测试

- 每一个Mapper.xml都需要在mybatis核心配置文件中注册

    ~~~xml
    <!--    每一个Mapper.xml都需要在mybatis核心配置文件中注册-->
    <mappers>
        <mapper resource="dao/UserMapper.xml"/>
    </mappers>
    ~~~

- 如果出现资源导出失败的问题

    ~~~xml
    <!--在build中配置resources，来防止我们资源导出失败的问题-->
    <build>
        <resources>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <!-- 不让过滤这些东西-->
                <filtering>true</filtering>
            </resource>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>
        </resources>
    </build>
    ~~~

- 测试代码

    > 关闭很重要，因为SqlSession的实例不是线程安全的

    ~~~java
    public class UserDaoTest {
        @Test
        public void test(){
            // 1. 获得SqlSession对象
            SqlSession sqlSession = MybatisUtils.getSqlSession();
            // 2. 执行sql 方式一：getMapper(推荐)
            //        UserDao mapper = sqlSession.getMapper(UserDao.class);
            //        List<User> userList = mapper.getUserList();
            // 执行sql 方式二 (不推荐了)
            List<User> userList = sqlSession.selectList("dao.UserDao.getUserList");
            for (User user:userList){
                System.out.println(user);
            }
    
            // 关闭SqlSession，很重要
            sqlSession.close();
        }
    }
    ~~~

## CRUD

- id : 就是对应的namespace中的方法名
- resultType：sql 语句执行的返回值
- parameterType：传递的参数类型

#### select

- 接口

    ~~~java
    // 查询全部用户
    public List<User> getUserList();
    
    // 根据ID 查询用户
    public User getUserById(int id);
    ~~~

- 实现，编写对应的mapper中的sql语句

    ~~~xml
    <select id="getUserList" resultType="pojo.User">
        select * from user
    </select>
    <select id="getUserById" resultType="pojo.User">
        select * from user where id=#{id}
    </select>
    ~~~

- 测试

    ~~~java
    @Test
    public void test(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        List<User> userList = mapper.getUserList();
        for (User user:userList){
            System.out.println(user);
        }
        sqlSession.close();
    }
    @Test
    public void test1(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        User user = mapper.getUserById(1);
        System.out.println(user);
        sqlSession.close();
    }
    ~~~

#### insert（需要提交事务才能生效）

- 接口

    ~~~java
    // insert 一个用户
    public int addUser(User user);
    ~~~

- 实现，编写对应的mapper中的sql语句

    ~~~xml
    <!--    对象中的属性，可以直接取出来-->
    <insert id="addUser" parameterType="pojo.User">
        insert into user (id, name, password)
        values (#{id},#{name},#{password});
    </insert>
    ~~~

- 测试

    ~~~java
    @Test
    public void test2(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        mapper.addUser(new User(4,"nih","123123"));
        sqlSession.commit();    // 提交事务
        sqlSession.close();
    }
    ~~~

#### update（需要提交事务才能生效）

- 接口

    ~~~java
    // 修改用户
    public int updateUser(User user);
    ~~~

- 实现，编写对应的mapper中的sql语句

    ~~~xml
    <update id="updateUser" parameterType="pojo.User">
        update user set name = #{name}  where id = #{id};
    </update>
    ~~~

- 测试

    ~~~java
    @Test
    public void test3(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        User user = new User();
        user.setId(4);
        user.setName("haha");
        mapper.updateUser(user);
        sqlSession.commit();    // 提交事务
        sqlSession.close();
    }
    ~~~

#### delete（需要提交事务才能生效）

- 接口

    ~~~java
    // 删除用户
    public int deleteUser(int id);
    ~~~

- 实现，编写对应的mapper中的sql语句

    ~~~xml
    <delete id="deleteUser" parameterType="int">
        delete from user where id = #{id};
    </delete>
    ~~~

- 测试

    ~~~java
    @Test
    public void test4(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        mapper.deleteUser(4);
        sqlSession.commit();
        sqlSession.close();
    }
    ~~~

#### 万能 Map

假设，我们的实体类，或者数据库中的表，字段或者参数过多，应当考虑使用map

- 接口

    ```java
    // 万能的Map
    public int updateUser2(Map<String,Object> map);
    ```

- 实现，编写对应的mapper中的sql语句

    ```xml
    <!--传递的map 的key-->
    <update id="updateUser2" parameterType="map">
    update user set name = #{name} where id = #{userid}
    </update>
    ```

- 测试

    ```java
    @Test
    public void test5(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    HashMap<String, Object> map = new HashMap<>();
    map.put("name","一二三");
    map.put("userid",1);
    mapper.updateUser2(map);
    sqlSession.commit();
    sqlSession.close();
    }
    ```

#### 模糊查询

- Java 代码执行的时候，传递通配符 % %

    ~~~java
    List<User> userList = mapper.getUserLike("%张%");
    ~~~

- 在 sql 拼接中使用通配符(写死)

    ~~~java
    select * from user where name like "%"#{value}"%"
    ~~~

## 配置解析

#### 核心配置文件

- mybatis-config.xml（官方建议起的名字）

- MyBatis 的配置文件包含了会深深影响 MyBatis 行为的设置和属性信息。 配置文档的顶层结构如下：

    ~~~xml
    configuration（配置）
    properties（属性）
    settings（设置）
    typeAliases（类型别名）
    typeHandlers（类型处理器）
    objectFactory（对象工厂）
    plugins（插件）
    environments（环境配置）
    environment（环境变量）
    transactionManager（事务管理器）
    dataSource（数据源）
    databaseIdProvider（数据库厂商标识）
    mappers（映射器）
    ~~~

#### 环境配置（environments）

- MyBatis 可以配置成适应多种环境，但每个SqlSessionFactory实例只能选择一种环境。每个数据库对应一个SqlSessionFactory实例
- Mybatis默认的事务管理器是 JDBC，还有一个 MANAGED
- 数据源默认使用 **POOLED** (有池子)

#### 属性（properties）

- 可以通过properties 属性来实现引用配置文件

- 这些属性可以外部配置且可动态替换的。可以在典型的 Java属性文件中配置，也可以通过properties元素的子元素来传递

    ~~~properties
    #编写一个配置文件  db.properties
    driver = com.mysql.cj.jdbc.Driver
    url1=jdbc:mysql://localhost:3306/mybatis?useUnicode=true&characterEncoding=utf8&useSSl=true
    username=root
    password=123456
    ~~~
    
    ~~~xml
    <!--在核心配置文件中引入-->
    <properties resource="db.properties"/>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url1}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
    ~~~
    
    ~~~xml
    <!--也可以在properties属性中引入，但外部优先级更高-->
    <properties resource="db.properties">
        <property name="username" value="root"/>
        <property name="password" value="123456"/>
    </properties>
    ~~~
    
- 注意点：

    - 配置文件中的起名 url可能存在问题，上面改为了(url1)
- 如果两个文件有同一个字段，优先会使用外部配置文件

#### 类型别名（typeAliases）

- 类型别名可为 Java 类型设置一个缩写名字
- 存在的意义降低冗余的全限定类名书写

~~~xml
<!--之前-->
<select id="getUserList" resultType="pojo.User">
    select * from user
</select>
~~~

~~~xml
<!--起别名(在配置文件中)-->
<typeAliases>
    <!--具体到类名-->
    <typeAlias alias="User" type="pojo.User"/>
    <!--也可以只指定一个包名-->
    <package name="pojo"/>
</typeAliases>
~~~

~~~xml
<!--现在-->
<select id="getUserList" resultType="User">
    select * from user
</select>
~~~

指定包的时候也可以在实体类上添加注解，使用注解的名字

~~~java
package pojo;
import org.apache.ibatis.type.Alias;
@Alias("hello")
public class User{...}
~~~

~~~xml
<select id="getUserList" resultType="hello">
    select * from user
</select>
~~~

#### 设置（settings）

[mybatis – MyBatis 3 | 配置](https://mybatis.org/mybatis-3/zh/configuration.html#settings)

logImpl

#### 映射器（mappers）

方式一：（推荐使用）

~~~xml
<!-- 使用相对于类路径的资源引用 -->
<mappers>
  <mapper resource="dao/UserMapper.xml"/>
</mappers>
~~~

方式二： 使用class 文件绑定注册

~~~xml
<!-- 使用映射器接口实现类的完全限定类名 -->
<mappers>
    <mapper class="dao.UserMapper"></mapper>
</mappers>
~~~

注意：

- 接口和他的Mapper配置文件必须同名
- 接口和他的Mapper配置文件必须在同一个包下

方式三： 使用扫描包 进行绑定注册

~~~xml
<mappers>
    <package name="dao"/>
</mappers>
~~~

注意：

- 接口和他的Mapper配置文件必须同名
- 接口和他的Mapper配置文件必须在同一个包下

#### 生命周期和作用域

生命周期和作用域是至关重要的，因为错误的使用会导致非常严重的**并发问题**

 **SqlSessionFactoryBuilder**

- 一旦创建了 SqlSessionFactoryBuilder，就不在需要它了
- 局部变量

**SqlSessionFactory**

- 可以想象为：数据库连接池
- SqlSessionFactory 一旦被创建就应该在应用的运行期间一直存在，**没有任何理由丢弃它或重新创建另一个实例**
- 最佳作用域是应用作用域
- 最简单的就是使用 单例模式 或者 静态单例模式

 **SqlSession**

- 连接到连接池的一个请求
- SqlSession 的实例不是线程安全的，因此不能被共享
- 最佳的作用域是请求或者方法作用域
- 用完之后需要第一时间关闭，否则资源可能被占用

**每一个Mapper 就代表一个具体的业务**

## 结果集映射

>  出现了属性名和字段名不一致的问题

查询的结果可能出现 **null**

解决方法：

- 起别名

~~~xml
<select id="getUserList" resultType="User">
    select id,name,password as pwd from user
</select>
~~~

#### resultMap

~~~xml
<!--结果集映射 id 代表select标签中的resultMap值，type代表之前你的resultType，也就是结果的返回类型-->
<resultMap id="UserMap" type="User">
    <!--column数据库中的字段，property实体类中的属性-->
    <!-- 可以只改不一样的-->
    <!-- <result column="id" property="id"/>-->
    <!-- <result column="username" property="username"/>-->
    <result column="password" property="pwd"/>
</resultMap>
<select id="getUserList" resultMap="UserMap">
    select * from user
</select>
~~~

- `resultMap` 元素是 MyBatis 中最重要最强大的元素
- ResultMap 的设计思想是，对简单的语句做到零配置，对于复杂一点的语句，只需要描述语句之间的关系就行了。

## 日志

LOG4J

STDOUT_LOGGING 

在 Mybatis 中具体使用哪一个日志实现，在设置中设定

在 mybatis  核心配置文件中，配置日志

#### **STDOUT_LOGGING**  标准日志工厂

~~~xml
<settings>
    <!--STDOUT_LOGGING是标准日志工厂，不用怎么配置-->
    <setting name="logImpl" value="STDOUT_LOGGING"/>
</settings>
~~~

#### LOG4J（有漏洞，不再使用）

- Log4j 是 apache的一个开源项目，通过使用Log4j，可以控制日志信息输出到控制台、文件、GUI组件
- 可以控制每一条日志的输出格式
- 通过定义每一条日志信息的级别，能够更加细致的控制日志生成过程
- 通过一个配置文件进行灵活的配置，不需要修改应用的代码

## 分页

#### limit分页

- 接口

    ~~~java
    // 分页
    List<User> getUserByLimit(Map<String,Integer> map);
    ~~~

- 实现

    ~~~xml
    <!--参数是Map类型所以填写map，Mybatis的规定基本类型前面带_,封装类型写成小写-->
    <select id="getUserByLimit" resultType="pojo.User" parameterType="map">
        select * from user limit #{startIndex},#{pageSize}
    </select>
    ~~~

- 测试

    ~~~java
    @Test
    public void test1(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        HashMap<String, Integer> map = new HashMap<>();
        map.put("startIndex",0);
        map.put("pageSize",2);
        List<User> userList = mapper.getUserByLimit(map);
        for (User user : userList) {
            System.out.println(user);
        }
        sqlSession.close();
    }
    ~~~

#### 分页插件

[MyBatis 分页插件 PageHelper](https://pagehelper.github.io/)

## 使用注解开发(mybatis最好不用)

​	使用注解来映射简单语句会使代码显得更加简洁，但对于稍微复杂一点的语句，Java 注解不仅力不从心，还会让你本就复杂的 SQL 语句更加混乱不堪。 因此，如果你需要做一些很 **复杂的操作**，最好 **用 XML 来映射语句**。

####  select

- 注解在接口上实现

    ~~~java
    // 使用注解查询全部用户
    @Select("select * from user")
    public List<User> getUserList();
    // 使用注解查询指定用户 @Param("和sql语句中的#{""}保持一致")
    // 参数前面必须加上@Param("")注解，如果多个参数：(@Param("id")int id,@Param("name")String name)
    @Select("select * from user where id = #{id}")
    public User getUserById(@Param("id")int id);
    ~~~

- 需要在核心配置文件中 

    ~~~xml
    <!--只用注册就可以了，不用写mapper.xml配置文件-->
    <mappers>
        <mapper class="dao.UserMapper" />
    </mappers>
    ~~~

- 测试

    ~~~java
    @Test
    public void test(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        // 底层主要应用反射
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        List<User> userList = mapper.getUserList();
        for (User user : userList) {
            System.out.println(user);
        }
        sqlSession.close();
    }
    @Test
    public void test2(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        User user = mapper.getUserById(1);
        System.out.println(user);
        sqlSession.close();
    }
    ~~~

#### insert



#### update



#### delete
