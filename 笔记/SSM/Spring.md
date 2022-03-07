**Spring 是一个轻量级的控制反转（IOC）和面向切面编程（AOP）的框架**

导包

~~~xml
<!--导这个包可以将其他包顺带导进来-->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.3.15</version>
</dependency>
~~~

## Hello Spring

- 创建一个实体类

    ~~~java
    package pojo;
    public class Hello {
        private String str;
        public Hello() {
        }
        public Hello(String str) {
            this.str = str;
        }
        public String getStr() {
            return str;
        }
        public void setStr(String str) {
            this.str = str;
        }
        @Override
        public String toString() {
            return "Hello{" +
                "str='" + str + '\'' +
                '}';
        }
    }
    ~~~

- 在resources文件下新建一个xxxx.xml 文件

    ~~~xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
            https://www.springframework.org/schema/beans/spring-beans.xsd">
        
        <!--使用Spring来创建对象，在Spring这些都成为Bean-->
        <!--
            类型   变量名 = new 类型();
            Hello hello = new Hello();
    
            id = 变量名
            class = new 的对象
            property 相当于給对象中的属性设置一个值
        -->
        <bean id="hello" class="pojo.Hello">
            <!--value是普通属性，ref是引用Spring容器中创建好的对象-->
            <property name="str" value=" Hello Spring"/>
        </bean>
    </beans>
    ~~~

- 测试

    ~~~java
    public class MyTest {
        public static void main(String[] args) {
            // 必须要写，是Spring容器。获取Spring的上下文对象 将配置文件的名字传入进去
            ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
            // 我们的对象现在都在Spring中管理了，我们要使用，去取出来就可以，获取的就是对象名(id="xxx")
            Hello hello = (Hello) context.getBean("hello");
            System.out.println(hello);
        }
    }
    ~~~

#### 思考

- Hello 对象是谁创建的

    hello 对象是由Spring创建

- Hello 对象的属性是怎么设置的

    hello 对象的属性由Spring容器设置

## IOC

> 所谓 IOC，一句话：对象由Spring 来创建，管理，装配

- 控制：谁来控制对象的创建，传统方式是由程序本身控制创建的，使用Spring后，由Spring来创建
- 反转：程序本身不创建对象，而变成被动的接受对象
- 依赖注入：就是利用set 方法来进行注入

#### IOC创建对象的方式

- 使用无参构造创建对象 默认

- 使用有参构造创建对象

    1. 下标赋值

        ~~~xml
        <!--下标赋值-->
        <bean id="hello" class="pojo.Hello">
            <constructor-arg index="0" value="你好" />
        </bean>
        ~~~

    2. 通过类型赋值(不建议)

        ~~~xml
        <!--类型赋值 如果是引用类型，需要全路径-->
        <bean id="hello" class="pojo.Hello">
            <constructor-arg type="java.lang.String" value="你好" />
        </bean>
        ~~~

    3. 参数名赋值(建议)

        ~~~xml
        <!--参数名赋值-->
        <bean id="hello" class="pojo.Hello">
            <constructor-arg name="str" value="你好" />
        </bean>
        ~~~

**在配置文件加载的时候，容器中管理的对象就已经被初始化了！**

## Spring 配置

**alias** 别名

> 给对象起外号

~~~xml
<!--别名，如果添加了别名，我们也可以使用别名获取到这个对象-->
<alias name="hello" alias="helloNew"/>
~~~

**Bean  的配置**

~~~
id 	 ：	bean 的唯一表示符，也就是相当于我们正常起的对象名
class：	bean 对象所对应的全限定名：包名+类型
name ：	也是起别名，但更加强大，可以同时取多个别名(可以空格逗号分号分隔)
~~~

**import**

一般用于团队开发使用，可以将多个配置文件，导入合并为一个

~~~xml
<import resource="xxx.xml" />
~~~

## 依赖注入

#### 构造器注入

~~~xml
<bean id="hello" class="pojo.Hello">
</bean>
~~~

#### Set 注入

环境搭建

~~~java
package pojo;
import java.util.*;
public class Student {
    private String name;
    private Hello hello;
    private String[] books;
    private List<String> hobbies;
    private Map<String,String> card;
    private Set<String> games;
    private String wife;
    private Properties info;
	// get、set方法此处省略...
}

~~~

**applicationContext.xml**

**普通值注入**

~~~xml
<!--普通值注入，value-->
<bean id="student" class="pojo.Student">
    <property name="name" value="zhang"/>
</bean>
~~~

**Bean注入**

~~~xml
<bean id="hello" class="pojo.Hello">
    <property name="str" value="nihao"/>
</bean>
<!--Bean注入，ref-->
<bean id="student" class="pojo.Student">
    <property name="hello" ref="hello"/>
</bean>
~~~

数组注入

~~~xml
<!--数组注入-->
<bean id="student" class="pojo.Student">
    <property name="books">
        <array>
            <value>一二三</value>
            <value>三二一</value>
        </array>
    </property>
</bean>
~~~

List注入

~~~xml
<!--List注入-->
<bean id="student" class="pojo.Student">
    <property name="hobbies">
        <list>
            <value>"sdf"</value>
            <value>"234"</value>
        </list>
    </property>
</bean>
~~~

Set注入

~~~xml
<!--Set注入-->
<bean id="student" class="pojo.Student">
    <property name="games">
        <set>
            <value>你好啊</value>
            <value>sdfsf</value>
        </set>
    </property>
</bean>
~~~

Map注入

~~~xml
<!--Map注入-->
<bean id="student" class="pojo.Student">
    <property name="card">
        <map>
            <entry key="身份证" value="123456"/>
            <entry key="手机号" value="24134534"/>
        </map>
    </property>
</bean>
~~~

Properties注入

~~~xml
<!--Properties注入-->
<bean id="student" class="pojo.Student">
    <property name="info">
        <props>
            <prop key="driver">comsdfsdlfjslf</prop>
            <prop key="url">hewoehfow</prop>
            <prop key="username">root</prop>
            <prop key="password">123456</prop>
        </props>
    </property>
</bean>
~~~

null值注入

~~~xml
<!--null 值注入-->
<bean id="student" class="pojo.Student">
    <property name="wife">
        <null/>
    </property>
</bean>
~~~

#### 扩展注入

p、c 命名空间注入 p表property c表constructor

[命名空间注入](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-p-namespace)

使用之前需要在 beans 头中添加约束

~~~xml
xmlns:p="http://www.springframework.org/schema/p"
xmlns:c="http://www.springframework.org/schema/c"
~~~

~~~xml
<bean id="hello" class="pojo.Hello">
    <property name="str" value="nihao"/>
</bean>
<!--p命名空间注入，可以直接注入属性的值，p代表着 property.等于上面-->
<bean id="hello" class="pojo.Hello" p:str="nihao"/>
~~~

~~~xml
<!--有参构造-->
<bean id="hello" class="pojo.Hello">
    <constructor-arg name="str" value="你好" />
</bean>
<!--c命名空间注入，可以直接注入有参构造的参数，c代表着constructor.等于上面-->
<bean id="hello" class="pojo.Hello" c:str="你好"/>
~~~

#### bean的作用域

1. 单例模式（Spring 默认机制）

    无论创建几个，实质上只有一个

    ~~~xml
    <bean ......... scope="Singleton"/>
    ~~~

2. 原型模式

    每次从容器中get的时候，都会产生一个新对象

    ~~~xml
    <bean ......... scope="Prototype"/>
    ~~~

3. 其余的 request、session、application 这些只在web开发中使用到

## 自动装配

- 自动装配是Spring满足bean依赖的一种方式
- Spring会在上下文自动寻找，并自动给bean装配属性

在Spring中有三种装配的方式

- 在xml中显式的配置
- 在Java中显式的配置
- 隐式的自动装配

**环境**

~~~java
class Dao{代码...}
class Cat{代码...}
class People{
    private Dao dog;
    private Cat cat;
    // set、get省略...
}
~~~

**在xml中显式的配置**

~~~xml
<bean id="dao" class="pojo.Dao" />
<bean id="cat" class="pojo.Cat" />
<bean id="people" class="pojo.People">
	<property name="dao" ref="dao" />
    <property name="cat" ref="cat" />
</bean>
~~~

**隐式的自动装配**

~~~xml
<bean id="dao" class="pojo.Dao" />
<bean id="cat" class="pojo.Cat" />
<!--byName:会自动在容器的上下文中查找，和自己对象set方法后面的值对应的beanid-->
<bean id="people" class="pojo.People" autowire="byName"/>
<!--byType:会自动在容器上下文中查找，和自己对象属性类型相同的bean-->
<bean id="people" class="pojo.People" autowire="byType"/>
~~~

总结：

- byName：需要保证所有的bean的id唯一，并且这个bean需要和自动注入的属性的set方法的值一致
- byType：需要保证所有的bean的class唯一，并且这个bean需要和自动注入的属性的类型一致

#### 注解自动装配

要想使用注解需要**导入约束并配置注解的支持**

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           https://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context
                           https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

</beans>
~~~

**@Autowired**

可以直接在属性上，也可以在set方法上使用，使用 Autowired 可以不写 set方法了。前提是在IOC容器中要注入待装配的bean，且符合byName

Autowired 会优先根据类型进行注入，如果容器中有多个满足类型的实例，就会根据Id进行注入

~~~java
class Dao{代码...}
class Cat{代码...}
class People{
    @Autowired
    private Dao dog;
    @Autowired
    private Cat cat;
    // set、get省略...
}
~~~

如果自动装配的环境比较复杂，自动装配无法通过【@Autowired】完成的时候，可以使用 **@Qualifier(value="xxx")**去配合@Autowired使用，用于指定唯一的bean对象注入

**@Nullable**

如果字段标记了这个注解，说明这个字段可以为null

**@Resource 注解**

~~~java
class Dao{代码...}
class Cat{代码...}
class People{
    @Resource
    private Dao dog;
    @Resource(name="xxx")	// 可以指定名字
    private Cat cat;
    // set、get省略...
}
~~~

小结：

@Resource 和 @Autowired 的区别：

- 都是用来自动装配的，都可以放在属性字段上
- @Autowired 通过byType 的方式实现，类型相同，则通过byName实现
- @Resource 通过byName的方式实现，找不到名字，则通过byType实现

## 注解开发

1. 要想使用注解开发，需要保证导入 aop 包
2. 需要导入 context约束，增加注解的支持

~~~xml
<!--指定要扫描的包，这个包下的注解就会生效-->
<context:component-scan base-package="pojo" />
<context:annotation-config/>
~~~

**bean**

- @Autowired：自动装配，通过类型-->名字

    - 如果@Autowired不能唯一自动装配上属性，则需要通过@Qualifier(value="xxx")

- @Nullable：字段标记了这个注解，说明这个字段可以为null

- @Resource：自动装配，通过名字-->类型

- @Component：组件，放在类上，说明这个类被Spring管理了

    ~~~java
    // 等价于     <bean id="user" class="pojo.User"/>
    @Component
    public class User {
        // 代码...
    }
    ~~~

**属性注入**

- @Value：給属性赋值

    ~~~java
    // 等价于 <property name="name" value="zhang"/>
    @Value("zhang")
    public void setName(String name) {
        this.name = name;
    }
    ~~~

**衍生注解**

@Component 有几个衍生注解，在web开发中，会按照mvc三层架构分层

- dao		    【@Repository】
- service      【@Service】
- controller 【@Controller】

这四个注解功能一致，都是代表将某个类注册到Spring中，装配Bean

**作用域**

@Scope("Singleton")：单例

@Scope("Prototype")：原型



xml 和 注解：

- xml 更加万能，适合任何场合，维护简单
- 注解不是自己类使用不了，维护相对复杂

最佳组合：

- xml用来管理bean
- 注解负责完成属性的注入

#### 使用Java的方式配置Spring

