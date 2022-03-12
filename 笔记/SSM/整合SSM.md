## 准备

1. 导包：

~~~xml
<dependencies>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.27</version>
    </dependency>
    <!-- 此处使用c3p0连接池 -->
    <dependency>
        <groupId>com.mchange</groupId>
        <artifactId>c3p0</artifactId>
        <version>0.9.5.5</version>
    </dependency>
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>servlet-api</artifactId>
        <version>2.5</version>
    </dependency>
    <dependency>
        <groupId>javax.servlet.jsp</groupId>
        <artifactId>jsp-api</artifactId>
        <version>2.2</version>
    </dependency>
    <dependency>
        <groupId>javax.servlet.jsp.jstl</groupId>
        <artifactId>jstl</artifactId>
        <version>1.2</version>
    </dependency>
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.7</version>
    </dependency>
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis-spring</artifactId>
        <version>2.0.7</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.3.15</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-jdbc</artifactId>
        <version>5.3.15</version>
    </dependency>
</dependencies>
~~~

2. 连接数据库及数据库文件配置	database.properties

    ~~~bash
    driver = com.mysql.cj.jdbc.Driver
    url=jdbc:mysql://localhost:3306/ssmbuild?useUnicode=true&characterEncoding=utf8&useSSl=true
    username=root
    password=123456
    ~~~

3. mybatis-config.xml 配置

    ~~~xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE configuration
            PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-config.dtd">
    <!--Mybatis核心配置文件-->
    <configuration>
    
    </configuration>
    ~~~

4. springxxx.xml 配置

    ~~~xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
                               https://www.springframework.org/schema/beans/spring-beans.xsd">
    
    
    </beans>
    ~~~

5. 编写实体类，与数据库对应

#### 整合 Mybatis层

1.  dao 层接口编写

    ~~~java
    public interface BookMapper {
        // 查询全部的书
        List<Books> queryAllBook();
    	// 增添改查....
    }
    ~~~

2. mybatisxml文件编写(sql语句)

    ~~~xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE mapper
            PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="dao.BookMapper">
        <select id="queryAllBook" resultType="pojo.Books">
            select * from books
        </select>
        <!--增添改查....-->
    </mapper>
    ~~~

3. 将编写好的mybatisxml注册到mybatis-config中

    ~~~xml
    <mappers>
        <mapper resource="dao/BookMapper.xml"/>
    </mappers>
    ~~~

4. service 层接口编写

    ~~~java
    public interface BookService {
        // 查询全部的书
        List<Books> queryAllBook();
    	// 增添改查...
    }
    ~~~

5. 编写service 接口的实现类

    ~~~java
    public class BookServiceImpl implements BookService{
        // 业务层调dao 层
        private BookMapper bookMapper;// set注入
    
        public void setBookMapper(BookMapper bookMapper) {
            this.bookMapper = bookMapper;
        }
        @Override
        public List<Books> queryAllBook() {
            return bookMapper.queryAllBook();
        }
        // 业务....
    }
    ~~~

#### 整合Spring层

1. spring-dao.xml

    ~~~xml
    <!--1. 关联数据库配置文件-->
    <context:property-placeholder location="classpath:database.properties"/>
    <!--2. 连接池，此处用的是c3p0-->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="${driver}"/>
        <property name="jdbcUrl" value="${url}"/>
        <property name="user" value="${username}"/>
        <property name="password" value="${password}"/>
    </bean>
    <!--3. sqlSessionFactory-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <!--绑定mybatis配置文件-->
        <property name="configLocation" value="mybatis-config.xml"/>
    </bean>
    
    <!--配置dao接口扫描包，动态的实现Dao接口可以注入到Spring容器中.代替了給接口加实现类-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <!--注入 SQLSessionFactory-->
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
        <!--要扫描的Dao包-->
        <property name="basePackage" value="dao" />
    </bean>
    ~~~

2. spring-service.xml

    ~~~xml
    <!--1.扫描service的包-->
    <context:component-scan base-package="service"/>
    <!--2.将所有业务类，注入到Spring中,此处用xml配置-->
    <bean id="BookServiceImpl" class="service.BookServiceImpl">
        <property name="bookMapper" ref="bookMapper"/>
    </bean>
    
    <!--3. 声明式事务配置-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <!--注入数据源-->
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <!--4. aop事务支持-->
    ~~~

3. applicationContext.xml

    ~~~xml
    <import resource="spring-dao.xml"/>
    <import resource="spring-service.xml"/>
    ~~~

#### 整合SpringMVC层

1.  添加一个 web框架支持

2. 在web.xml中配置 控制器DispatcherServlet

    ~~~XML
    <!--DispatcherServlet-->
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:applicationContext.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
    
    <!--乱码过滤-->
    <filter>
        <filter-name>encodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>encodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
    
    <!--Session设置15分钟失效-->
    <session-config>
        <session-timeout>15</session-timeout>
    </session-config>
    ~~~

3. 配置springxxxx.xml的mvc三大件：处理映射器、处理适配器、视图解析器

    ~~~xml
    <!--1.注解驱动-->
    <mvc:annotation-driven/>
    <!--2.静态资源过滤-->
    <mvc:default-servlet-handler/>
    <!--3.扫描包-->
    <context:component-scan base-package="controller"/>
    
    <!--4.视图解析器-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
    ~~~

## CRUD

