**什么是JPA**

> 由SUN官方退出的。JPA 是JDBC的升级，是一组由规范接口。是由==ORM框架==实现
>
> ORM：O:Object R: Relational M:mapping

**Hibernate与JPA**

底层需要某种实现，而Hibernate就是实现了JPA接口的ORM框架。
也就是说：JPA是一套ORM规范，Hibernate实现了JPA规范！

**mybatis和hibernate区别**

- mybatis：小巧、方便、高效、简单、直接、半自动（需手写sql）
- hibernate：强大、方便、高效、复杂、绕弯子、全自动

## Hibernate示例

pom.xml

~~~xml
<!--        hibernate对jpa的支持包-->
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-entitymanager</artifactId>
    <version>5.4.32.Final</version>
</dependency>
<!--Mysql and MariaDB-->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.27</version>
</dependency>
~~~

实体类

~~~java
package com.zyh.pojo;
import javax.persistence.*;
@Entity     // hibernate实体类  
@Table(name = "cst_customer")// 映射的表名
public class Customer {
    /**
     * @Id：声明主键的配置
     * @GeneratedValue:配置主键的生成策略
     * strategy
     * GenerationType.IDENTITY ：自增，mysql
     * * 底层数据库必须支持自动增长（底层数据库支持的自动增长方式，对id自增）
     * GenerationType.SEQUENCE : 序列，oracle
     * * 底层数据库必须支持序列
     * GenerationType.TABLE : jpa提供的一种机制，通过一张数据库表的形式帮助我们完成主键自增
     * GenerationType.AUTO ： 由程序自动的帮助我们选择主键生成策略
     * @Column:配置属性和字段的映射关系
     * name：数据库表中字段的名称
     */
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "cust_id")
    private Long custId; //客户的主键
    @Column(name = "cust_name")
    private String custName;//客户名称
    @Column(name="cust_address")
    private String custAddress;//客户地址
}

~~~

hibernate.cfg.xml

~~~xml
<?xml version='1.0' encoding='utf-8'?>
<!DOCTYPE hibernate-configuration PUBLIC
        "-//Hibernate/Hibernate Configuration DTD//EN"
        "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
<hibernate-configuration>
    <session-factory>
        <!--        配置数据库连接信息-->
        <property name="connection.driver_class">com.mysql.cj.jdbc.Driver</property>
        <property name="connection.url">jdbc:mysql://localhost:3306/springdata_jpa?useUnicode=true;characterEncoding=utf8;useSSl=true</property>
        <property name="connection.username">root</property>
        <property name="connection.password">123456</property>

        <!--会在日志中记录sql 默认false-->
        <property name="show_sql">true</property>
        <!--        是否格式化sql 默认false-->
        <property name="format_sql">true</property>
        <!--表生成策略
            默认 none 不自动生成
            update  如果没有表会创建，有会检查更新
            create   创建-->
        <property name="hbm2ddl.auto">update</property>
        <!--配置方言：选择数据库类型 查找dialect类，查看其实现，找到匹配的数据库此处是mysqlInnoDB引擎-->
        <property name="dialect">org.hibernate.dialect.MySQL5InnoDBDialect</property>
        <!--         映射方式-->
        <mapping class="com.zyh.pojo.Customer"></mapping>

    </session-factory>
</hibernate-configuration>
~~~

测试

~~~java
package com.zyh.test;
import com.zyh.pojo.Customer;
import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.Transaction;
import org.hibernate.boot.MetadataSources;
import org.hibernate.boot.registry.StandardServiceRegistry;
import org.hibernate.boot.registry.StandardServiceRegistryBuilder;
import org.junit.Before;
import org.junit.Test;
public class hibernateTest {
    private SessionFactory sf;
    @Before
    public void init(){
        StandardServiceRegistry registry = new StandardServiceRegistryBuilder().configure("/hibernate.cfg.xml").build();
        // 根据服务注册类创建一个元数据资源集，同时构建元数据并生成应用一般唯一的的session工厂

        sf = new MetadataSources(registry).buildMetadata().buildSessionFactory();
    }
 @Test
    public void test(){
        // 进行持久化操作
        Session session = sf.openSession();
        Transaction tx = session.beginTransaction();
        
        Customer customer = new Customer();
        customer.setCustName("test");
        session.save(customer); // 增

//        session.find(Customer.class,1L);    // 查询
//        session.load(Customer.class,1L);    // 懒加载查询即用到再去查

//        customer.setCustId(1L);
//        session.saveOrUpdate(customer);// 如果id存在就更新，不存在就新增

//        customer.setCustId(1L);
//        session.remove(customer);     // 删除

        tx.commit();
    }
}

~~~

**hql**

~~~java
@Test
public void testHql(){
    Session session = sf.openSession();
    Transaction tx = session.beginTransaction();
    String hql = "FROM Customer where custId=:id";
    List<Customer> list = session.createQuery(hql, Customer.class).setParameter("id", 2L).getResultList();
    System.out.println(list);
    tx.commit();
}
~~~

## jpa示例

pom.xml 、 实体类同上

resources/META-INF文件夹下新建persistence.xml

~~~xml
<?xml version="1.0" encoding="UTF-8" ?>
<persistence xmlns="http://xmlns.jcp.org/xml/ns/persistence" version="2.1">
    <!--需要配置persistence‐unit节点-->
    <!--      持久化单元：-->
    <!--      nam持久化单元名称-->
    <!--      transaction‐type：事务管理的方式-->
    <!--      JTA：分布式事务管理8 RESOURCE_LOCAL：本地事务管理-->
    <persistence-unit name="hibernateJPA" transaction-type="RESOURCE_LOCAL">
        <!--      jpa的实现方式-->
        <provider>org.hibernate.jpa.HibernatePersistenceProvider</provider>
        <!--          需要进行ORM的POJO类-->
        <class>com.zyh.pojo.Customer</class>

        <!--      可选配置：配置jpa实现方的配置信息-->
        <properties>
            <!--      数据库信息-->
            <!--      用户名，javax.persistence.jdbc.user-->
            <!--      密码， javax.persistence.jdbc.password-->
            <!--      驱动， javax.persistence.jdbc.driver-->
            <!--      数据库地址 javax.persistence.jdbc.url-->

            <property name="javax.persistence.jdbc.user" value="root"/>
            <property name="javax.persistence.jdbc.password" value="123456"/>
            <property name="javax.persistence.jdbc.driver" value="com.mysql.cj.jdbc.Driver"/>
            <property name="javax.persistence.jdbc.url" value="jdbc:mysql://localhost:3306/springdata_jpa?useUnicode=true;characterEncoding=utf8;useSSl=true"/>

            <!--      配置jpa实现方(hibernate)的配置信息-->
            <!--      显示sql ： false|true-->
            <!--      自动创建数据库表 ： hibernate.hbm2ddl.auto-->
            <!--      create : 程序运行时创建数据库表（如果有表，先删除表再创建）-->
            <!--      update ：程序运行时创建表（如果有表，不会创建表）-->
            <!--      none ：不会创建表-->
            <property name="hibernate.show_sql" value="true" />
            <property name="hibernate.hbm2ddl.auto" value="update" />
            <property name="hibernate.dialect" value="org.hibernate.dialect.MySQL5InnoDBDialect" />

        </properties>
    </persistence-unit>
</persistence>
~~~

测试

~~~java
package com.zyh.test;

import com.zyh.pojo.Customer;
import org.junit.Before;
import org.junit.Test;

import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.EntityTransaction;
import javax.persistence.Persistence;

public class JpaTest {

    EntityManagerFactory factory;

    @Before
    public void before(){
        factory = Persistence.createEntityManagerFactory("hibernateJPA");
    }

    @Test
    public void test(){
        EntityManager entityManager = factory.createEntityManager();
        EntityTransaction transaction = entityManager.getTransaction();
        transaction.begin();
        Customer customer = new Customer();
        customer.setCustName("zhangsan");
        entityManager.persist(customer);
        transaction.commit();
    }
}
~~~

#### JPA对象的四种状态

- **临时状态**：刚创建出来，∙没有与entityManager发生关系，没有被持久化，不处于entityManager中的对象
- **持久状态**：∙与entityManager发生关系，已经被持久化(比如从数据库中查询出来的数据)。**处于持久状态的数据被修改会直接同步数据库**
- **删除状态**：执行remove方法，事物提交之前
- **游离状态**：游离状态就是提交到数据库后，事务commit后实体的状态，因为事务已经提交了，此时实体的属性任你如何改变，也不会同步到数据库，因为游离是没人管的孩子，不在持久化上下文中。

## SpringData Jpa

SpringData Jpa 极大简化了数据库访问层代码。 如何简化的呢？ 使用SpringDataJpa，我们的dao层中只需要写接口，就自动具有了增删改查、分页查询等方法。

#### xml方式

1.依赖 

~~~xml
<!--父工程中添加的依赖-->
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.data</groupId>
            <artifactId>spring-data-bom</artifactId>
            <version>2021.2.5</version>
            <scope>import</scope>
            <type>pom</type>
        </dependency>
    </dependencies>
</dependencyManagement>
~~~

~~~xml
<dependency>
    <groupId>org.springframework.data</groupId>
    <artifactId>spring-data-jpa</artifactId>
</dependency>
<!--        hibernate对jpa的支持包-->
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-entitymanager</artifactId>
    <version>5.4.32.Final</version>
</dependency>
<!--Mysql and MariaDB-->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.27</version>
</dependency>
<!--        连接池-->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.2.8</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>5.3.10</version>
    <scope>test</scope>
</dependency>
~~~

2. resources下新建spring.xml

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:jpa="http://www.springframework.org/schema/data/jpa" xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           https://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/data/jpa
                           https://www.springframework.org/schema/data/jpa/spring-jpa.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">

    <!--用于整合JPA @EnableJpaRepositories-->
    <jpa:repositories base-package="com.zyh.dao"
                      entity-manager-factory-ref="entityManagerFactory"
                      transaction-manager-ref="jpaTransactionManager"/>

    <!-- 2.配置entityManagerFactory -->
    <bean name="entityManagerFactory" class="org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean">
        <property name="jpaVendorAdapter">
            <!--Hibernate 实现-->
            <bean class="org.springframework.orm.jpa.vendor.HibernateJpaVendorAdapter ">
                <!--表生成策略 true 生成
                <property name="hbm2ddl.auto">update</property> 和hibernate.cfg.xml一样
                -->
                <property name="generateDdl" value="true"></property>
                <property name="showSql" value="true"></property>
            </bean>
        </property>
        <!--设置实体类的包以及数据源-->
        <property name="packagesToScan" value="com.zyh.pojo"></property>
        <property name="dataSource" ref="dataSource"></property>
    </bean>
    <!-- druid连接池-->
    <bean class="com.alibaba.druid.pool.DruidDataSource" name="dataSource">
        <property name="url" value="jdbc:mysql://localhost:3306/springdata_jpa?useUnicode=true;characterEncoding=utf8;useSSl=true"></property>
        <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"></property>
        <property name="username" value="root"></property>
        <property name="password" value="123456"></property>
    </bean>
    <!-- 3.事务管理器-->
    <bean class="org.springframework.orm.jpa.JpaTransactionManager" name="jpaTransactionManager">
        <property name="entityManagerFactory" ref="entityManagerFactory"></property>
    </bean>
    <!--  选用springframework...tx的
           启动注解方式的声明式事务-->
    <tx:annotation-driven transaction-manager="jpaTransactionManager"></tx:annotation-driven>
</beans>
~~~

3. pojo实体类

~~~java
package com.zyh.pojo;

import javax.persistence.*;

@Entity     // hibernate实体类
@Table(name = "cst_customer")// 映射的表名
public class Customer {
    /**
     * @Id：声明主键的配置
     * @GeneratedValue:配置主键的生成策略
     * strategy
     * GenerationType.IDENTITY ：自增，mysql
     * * 底层数据库必须支持自动增长（底层数据库支持的自动增长方式，对id自增）
     * GenerationType.SEQUENCE : 序列，oracle
     * * 底层数据库必须支持序列
     * GenerationType.TABLE : jpa提供的一种机制，通过一张数据库表的形式帮助我们完成主键自增
     * GenerationType.AUTO ： 由程序自动的帮助我们选择主键生成策略
     * @Column:配置属性和字段的映射关系
     * name：数据库表中字段的名称
     */
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "cust_id")
    private Long custId; //客户的主键
    @Column(name = "cust_name")
    private String custName;//客户名称
    @Column(name="cust_address")
    private String custAddress;//客户地址
}
~~~

4. CustomerRepository			--dao层

~~~java
package com.zyh.dao;
import com.zyh.pojo.Customer;
import org.springframework.data.repository.CrudRepository;
public interface CustomerRepository extends CrudRepository<Customer,Long> {
}
~~~

5. 测试

~~~java
// 基于Junit4 spring单元测试
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("/spring.xml")
public class SpringDataJpaTest {
    @Autowired
    private CustomerRepository repository;
    @Test
    public void test(){
        Iterable<Customer> all = repository.findAll();
        for (Customer customer : all) {
            System.out.println(customer);
        }
    }
}
~~~

 #### 注解方式(javaConfig)

[Spring Data JPA - Reference Documentation](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.java-config)

1. 导入依赖

同上方xml方式

2. 新建一个配置类

~~~java
@Configuration      // 标记当前类为配置类 = xml配置类
@EnableJpaRepositories(basePackages="com.zyh.dao")  // 启动jps = <jpa:repositories base-package="com.zyh.dao"
@EnableTransactionManagement    // 开启事务
public class ApplicationConfig {
    @Bean
    public DruidDataSource dataSource() {
        // 设置数据源 -- druid
        DruidDataSource druidDataSource = new DruidDataSource();
        druidDataSource.setDriverClassName("com.mysql.cj.jdbc.Driver");
        druidDataSource.setUrl("jdbc:mysql://localhost:3306/springdata_jpa?useUnicode=true;characterEncoding=utf8;useSSl=true");
        druidDataSource.setUsername("root");
        druidDataSource.setPassword("123456");
        return druidDataSource;
    }
    @Bean
    public LocalContainerEntityManagerFactoryBean entityManagerFactory() {
        HibernateJpaVendorAdapter vendorAdapter = new HibernateJpaVendorAdapter();
        vendorAdapter.setShowSql(true); // 显示sql
        vendorAdapter.setGenerateDdl(true);
        LocalContainerEntityManagerFactoryBean factory = new LocalContainerEntityManagerFactoryBean();
        factory.setJpaVendorAdapter(vendorAdapter);
        factory.setPackagesToScan("com.zyh.pojo");// 实体类
        factory.setDataSource(dataSource());
        return factory;
    }
    @Bean
    public PlatformTransactionManager transactionManager(EntityManagerFactory entityManagerFactory) {
        JpaTransactionManager txManager = new JpaTransactionManager();
        txManager.setEntityManagerFactory(entityManagerFactory);
        return txManager;
    }
}@Configuration      // 标记当前类为配置类 = xml配置类
@EnableJpaRepositories(basePackages="com.zyh.dao")  // 启动jps = <jpa:repositories base-package="com.zyh.dao"
@EnableTransactionManagement    // 开启事务
class ApplicationConfig {
    @Bean
    public DruidDataSource dataSource() {
        // 设置数据源 -- druid
        DruidDataSource druidDataSource = new DruidDataSource();
        druidDataSource.setDriverClassName("com.mysql.cj.jdbc.Driver");
        druidDataSource.setUrl("jdbc:mysql://localhost:3306/springdata_jpa?useUnicode=true;characterEncoding=utf8;useSSl=true");
        druidDataSource.setUsername("root");
        druidDataSource.setPassword("123456");
        return druidDataSource;
    }
    @Bean
    public LocalContainerEntityManagerFactoryBean entityManagerFactory() {
        HibernateJpaVendorAdapter vendorAdapter = new HibernateJpaVendorAdapter();
        vendorAdapter.setGenerateDdl(true);
        LocalContainerEntityManagerFactoryBean factory = new LocalContainerEntityManagerFactoryBean();
        factory.setJpaVendorAdapter(vendorAdapter);
        factory.setPackagesToScan("com.zyh.pojo");// 实体类
        factory.setDataSource(dataSource());
        return factory;
    }
    @Bean
    public PlatformTransactionManager transactionManager(EntityManagerFactory entityManagerFactory) {
        JpaTransactionManager txManager = new JpaTransactionManager();
        txManager.setEntityManagerFactory(entityManagerFactory);
        return txManager;
    }
}
~~~

3. pojo实体类

同上方xml方式

4. CustomerRepository			--dao层

同上方xml方式

5. 测试

~~~java
// 基于Junit4 spring单元测试
@RunWith(SpringJUnit4ClassRunner.class)
//@ContextConfiguration("/spring.xml")
@ContextConfiguration(classes = ApplicationConfig.class)
public class SpringDataJpaTest { // 同上方xml方式
}
~~~

#### 分页

dao层继承接口 PagingAndSortingRepository

~~~java
public interface CustomerRepository extends PagingAndSortingRepository<Customer,Long> {

}
~~~

测试

~~~java
Page<Customer> all = repository.findAll(PageRequest.of(0, 3));
Iterable<Customer> all1 = repository.findAll();
System.out.println(all.getContent());
~~~

## 自定义操作

**查询**

~~~java
public interface CustomerRepository extends PagingAndSortingRepository<Customer,Long> {
    // 查询	参数设置方式
    @Query("from Customer where custName like %?1%")  // 模糊查询
    @Query("from Customer where custName=?1")	// 1.索引    ?数字
    @Query("from Customer where custName=:custName")	// 2.具名		:参数名 结合@Param注解指定参数名
    List<Customer> findCustomerByCustomName(@Param("custName" )String custName);
}
~~~

~~~java
@Test
public void testR(){
    List<Customer> test = repository.findCustomerByCustomName("test");
    System.out.println(test);
}
~~~

**修改**	需要給Customer取别名

~~~java
// 修改
@Transactional  // 增删改需要事务支持，正常卸载service，此处为了方便
@Modifying  // 通知springJpa是增删改的操作
@Query(value = "update Customer c set c.custName=:custName where c.custId=:id")
Integer updateCustomerById(@Param("custName") String custName,@Param("id")Long id);
~~~

~~~java
@Test
public void testU(){
    repository.updateCustomerById("test2", 2L);
}
~~~

**删除**		需要給Customer取别名

~~~java
// 删除
@Transactional  // 增删改需要事务支持，正常卸载service，此处为了方便
@Modifying  // 通知springJpa是增删改的操作
@Query("delete from Customer c where c.custId=:id")
Integer deleteCustomer(@Param("id")Long id);
~~~

~~~java
@Test
public void testD(){
    repository.deleteCustomer( 2L);
}
~~~

新增		需要給Customer取别名

~~~java
// 新增	hibernate 是不支持insert的
@Transactional  // 增删改需要事务支持，正常卸载service，此处为了方便
@Modifying  // 通知springJpa是增删改的操作
@Query("Insert into Customer(custName) select c.custName from Customer c where c.custId=:id")
Integer insertCustomerBySelect(@Param("id")Long id);
~~~

~~~java
@Test
public void testC(){
    repository.insertCustomerBySelect(2L);
}
~~~

#### 原生sql

也就是需要加上 nativeQuery = true

~~~java
// 查询 -- 原生sql
@Query(value = "select * from cst_customer where cust_name=:name",nativeQuery = true)
List<Customer> findCustomerByCustomName1(@Param("name") String custName);
// 新增
@Transactional  // 增删改需要事务支持，正常卸载service，此处为了方便
@Modifying  // 通知springJpa是增删改的操作
@Query(value = "Insert into cst_customer(cust_name,cust_address) values(:name,:address)",nativeQuery = true)
Integer insertCustomerBySelect1(@Param("name")String custName,@Param("address")String custAddress);
~~~

~~~java
@Test
public void test1(){
    List<Customer> test = repository.findCustomerByCustomName1("test");
    System.out.println(test);
}
@Test
public void testC1(){
    repository.insertCustomerBySelect1("name","address");
}
~~~

#### 规定方法名

- 支持的查询方法主题关键字（前缀）
  - 决定当前方法的作用
  - 只支持查询和删除

![](.\images\SpringDataJpa01.png)

- 支持的查询方法谓词关键字和修饰符
  - 决定查询条件

![](.\images\SpringDataJpa02.png)

**举例**

~~~java
public interface CustomerRepository extends PagingAndSortingRepository<Customer,Long> {
    List<Customer> findAllByCustName(String custName);
    List<Customer> findAllByCustNameLike(String custName);
    // 增删改 需要加上事务支持以及通知springJpa
    @Transactional
    @Modifying
    int deleteByCustId(Long custid);
}
~~~

测试

~~~java
@Test
public void test01(){
    List<Customer> test = repository.findAllByCustName("test");
    System.out.println(test);
}
@Test
public void test02(){
    List<Customer> test = repository.findAllByCustNameLike("%t%");
    System.out.println(test);
}
@Test
public void test03(){
    int i = repository.deleteByCustId(2L);
}
~~~

#### 动态条件

###### Query By Example

- 只支持查询，不支持嵌套或分组的属性约束 （如 firstname=?0 or (firstname=?1 and lastname=?2)）
- 只支持字符串（比如日期这种就不支持）

dao层接口

~~~java
public interface CustomerQBERepository extends
    PagingAndSortingRepository<Customer,Long>
    , QueryByExampleExecutor<Customer> {

}
~~~

测试

~~~java
@Test
public void test01(){
    Customer customer = new Customer();
    customer.setCustName("徐庶");
    Example<Customer> example = Example.of(customer);
    System.out.println(repository.findAll(example));
}
~~~

~~~java
@Test
public void test01(){
    Customer customer = new Customer();
    customer.setCustName("徐庶");
    // 匹配器， 去设置更多条件匹配
    ExampleMatcher matcher = ExampleMatcher.matching()
//         .withIgnoreCase();		// 如果不写，就对所有属性忽略大小写
        .withIgnoreCase("custAddress");	// 对custAddress忽略大小写
    // Example 要导入org.springframework.data.domain中的
    Example<Customer> example = Example.of(customer,matcher);

    System.out.println(repository.findAll(example));
}

~~~

###### Specifications

- 在之前使用Query by Example只能针对字符串进行条件设置，那如果希望对所有类型支持，可以使用Specifications

~~~java
public interface CustomerRepository1 extends CrudRepository<Customer, Long>, JpaSpecificationExecutor<Customer> {

}
~~~

实现

~~~java

~~~

###### Querydsl

QueryDSL是基于ORM框架或SQL平台上的一个通用查询框架

