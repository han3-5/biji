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
    @Component // 等价于 <bean id="user" class="pojo.User"/>
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

不使用Spring的xml配置，由Java来做

JavaConfig 是Spring 的一个子项目，Spring4之后成为了核心功能

- 实体类

    ~~~java
    public class User {
        private String name;
        public String getName() {
            return name;
        }
        @Value("z")
        public void setName(String name) {
            this.name = name;
        }
    }
    ~~~

- JavaConfig

    ~~~java
    // @Configuration 代表这是一个配置类，和之前配置的 .xml<beans> 一样
    @Configuration  // 这个也会被Spring容器托管，注册到容器中，因为它本来就是一个@Component
    //@Import(xxx.class)  // 可以将别的@Configuration 类导入进来
    public class ZConfig {
    
        // 注册一个bean ，相当于之前写的<bean />
        // 方法的名字，相当于bean标签中的id属性
        // 方法的返回值，相当于bean标签中的class属性
        @Bean   // @Bean(name = "可以起别名")
        public User getUser(){
            return new User();  // 返回要注入到bean的对象
        }
    }
    ~~~

- 测试

    ~~~java
    public class MyTest {
        public static void main(String[] args) {
            ApplicationContext context = new AnnotationConfigApplicationContext(ZConfig.class);
            User bean = (User) context.getBean("getUser");
            System.out.println(bean.getName());
        }
    }
    ~~~

**组件扫描**

- 实体类

~~~java
@Component	
public class Stu {
    @Value("zzzz")
    private String  name;
    private int age;
    private String sex;
    // 省略get,set...
}
~~~

- 配置类

~~~java
@Configuration
@ComponentScan("test")
public class StuConfig {
}
~~~

- 测试

```java
ApplicationContext context = new AnnotationConfigApplicationContext(StuConfig.class);
Stu stu = context.getBean("stu", Stu.class);
System.out.println(stu);
```

组件扫描和@Bean方式保存对象的区别：

1. @Bean可以手动选择调用哪个构造方法，组件扫描默认调用无参构造
2. @Bean有机会给对象的属性赋值，组件扫描方式没有（只能在声明属性的时候赋值 或者 编写无参构造时，在构造方法中为属性赋值）
3. 赋值的灵活性@Bean更好
4. 组件扫描的代码量少

## 代理模式

这是Spring AOP底层

#### 静态代理

- 抽象角色：一般会使用接口或者抽象类来表示
- 真实角色：被代理的角色
- 代理角色：代理真实的角色，代理后，会有一些附属的操作
- 客户：访问代理对象的人

静态代理好处：

- 可以使真实角色的操作更加纯粹，不用去关注一些公共的业务
- 公共的业务交给代理的角色，实现了业务的分工
- 公共业务发生扩展的时候，方便集中管理

缺点：

- 一个真实角色就会产生一个代理角色；代码量会翻倍，开发效率降低

#### 动态代理

> 全靠反射

- 动态代理和静态代理角色一样
- 动态代理的代理类是动态生成的，不是直接写好
- 动态代理分为两大类：基于接口的动态代理，基于类的动态代理
    - 基于接口 -- JDK 动态代理
    - 基于类：cglib
    - java字节码实现：javasist

需要了解两个类： **Proxy** （代理）和 **InvocationHandler**（调用处理程序）

**Proxy 用来生成动态代理实例**

**InvocationHandler 用来调用处理程序并返回结果**

- 抽象角色：一般会使用接口或者抽象类来表示

    ~~~java
    public interface Rent {
        public void rent();
    }
    ~~~

- 真实角色：被代理的角色

    ~~~java
    // 房东
    public class Host implements Rent{
        @Override
        public void rent() {
            System.out.println("房东出租房子");
        }
    }
    ~~~

- 代理角色：代理真实的角色，代理后，会有一些附属的操作

    ~~~java
    // 用这个类，自动生成代理类
    public class ProxyInvocationHandler implements InvocationHandler {
        // 被代理的接口
        private Rent rent;
        public void setRent(Rent rent){
            this.rent = rent;
        }
        // 生成得到代理类
        public Object getProxy(){
            return Proxy.newProxyInstance(this.getClass().getClassLoader(),
                                          rent.getClass().getInterfaces(),
                                          this);
        }
        // 处理代理实例，并返回结果
        @Override
        public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
            log(method.getName());  // 在执行代理对象方法前执行的一个附属操作
            // 动态代理的本质，就是使用反射机制
            Object result = method.invoke(rent, args);
            return result;
        }
        // 想增加一个附属操作
        public void log(String msg){
            System.out.println("执行了"+msg+"方法");
        }
    }
    ~~~

- 客户：访问代理对象的人

    ~~~java
    // 客户
    public class Client{
        public static void main(String[] args) {
            // 真实角色
            Host host = new Host();
    
            // 代理角色
            ProxyInvocationHandler proxyInvocationHandler = new ProxyInvocationHandler();
            // 通过调用程序处理角色来处理要调用的接口对象
            proxyInvocationHandler.setRent(host);
            // 这里的proxy 就是动态生成的
            Rent proxy = (Rent) proxyInvocationHandler.getProxy();
            proxy.rent();
        }
    }
    ~~~

动态代理的好处

- 有静态代理的所有好处
- 一个动态代理类代理的是一个接口，一般就是对应的一类业务
- 一个动态代理类可以代理多个类，只要是实现了同一个接口即可

## AOP

> aop 意味 面向切面编程，通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术

- 横切关注点：与业务逻辑无关，但需要关注的部分。如：日志，安全，缓存，事务等
- 切面（ASPECT）：横切关注点被模块化的特殊对象，是一个类
- 通知（Adivce）：切面必须要完成的工作，是类中的一个方法
- 目标（target）： 被通知的对象
- 代理（Proxy）： 向目标对象应用通知之后创建的对象
- 切入点（Pointcut）：切面通知执行的"地点"
- 连接点（JointPoint）：与切入点匹配的执行点

#### Spring 实现 AOP

导包

~~~xml
<!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.6</version>
</dependency>
~~~

#### **方法一**：使用Spring原生API 实现

> 主要是SpringApi接口实现

- 接口与实现类

    ~~~java
    public interface UserService {
        public void add();
    }
    public class UserServiceImpl implements UserService{
        @Override
        public void add() {
            System.out.println("增加了一个用户");
        }
    }
    ~~~

- 想切入的（切面）

    ~~~java
    package log;
    import org.springframework.aop.MethodBeforeAdvice;
    import java.lang.reflect.Method;
    public class Log implements MethodBeforeAdvice {
        // method ：要执行的目标对象的方法
        // args： 参数
        // target ：目标对象
        @Override
        public void before(Method method, Object[] args, Object target) throws Throwable {
            System.out.println(method.getName()+"被执行了");
        }
    }
    ~~~

    ~~~java
    package log;
    import org.springframework.aop.AfterReturningAdvice;
    import java.lang.reflect.Method;
    public class AfterLog implements AfterReturningAdvice {
        // returnValue：返回值
        @Override
        public void afterReturning(Object returnValue, Method method, Object[] args, Object target) throws Throwable {
            System.out.println("执行了"+method.getName()+"方法，返回结果为："+returnValue);
        }
    }
    ~~~

- 注册bean，实现aop

    ~~~xml
    <!--aop约束-->
    xmlns:aop="http://www.springframework.org/schema/aop"
    http://www.springframework.org/schema/aop
    https://www.springframework.org/schema/aop/spring-aop.xsd
    ~~~

    ~~~xml
    <!--注册bean-->
    <bean id="userService" class="service.UserServiceImpl"/>
    <bean id="log" class="log.Log"/>
    <bean id="afterLog" class="log.AfterLog"/>
    <!--方式一：使用原生Spring API接口-->
    <!--配置aop:需要导入aop的约束-->
    <aop:config>
        <!--切入点:id="名字" expression:表达式 execution(要执行的位置)-->
        <!--execution(修饰符 返回值 包.类.方法名(参数) throws异常) 【返回值方法名(参数)】不能省略-->
        <aop:pointcut id="pointcut123" expression="execution(* service.UserServiceImpl.*(..))"/>
        <!--执行环绕增加,advice-ref="将这个类"切入到pointcut-ref="待切入的类"-->
        <aop:advisor advice-ref="log" pointcut-ref="pointcut123"/>
        <aop:advisor advice-ref="afterLog" pointcut-ref="pointcut123"/>
    </aop:config>
    ~~~

- 测试

    ~~~java
    public class MytTest {
        public static void main(String[] args) {
            ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
            // 注意：动态代理 代理的是接口
            UserService userService = (UserService) context.getBean("userService");
            userService.add();
        }
    }
    ~~~

#### 方法二：自定义

> 主要是切面的定义

- 想切入的（切面）

    ~~~java
    public class DiyPointCut {
        public void before(){
            System.out.println("方法执行前...");
        }
        public void after(){
            System.out.println("方法执行后...");
        }
    }
    ~~~

- 注册bean，实现aop

    ~~~xml
    <!--aop约束-->
    xmlns:aop="http://www.springframework.org/schema/aop"
    http://www.springframework.org/schema/aop
    https://www.springframework.org/schema/aop/spring-aop.xsd
    ~~~

    ~~~xml
    <!--注册bean-->
    <bean id="diy" class="div.DiyPointCut"/>
    <!--方式二：自定义-->
    <aop:config>
        <!--自定义切面 ref="要引用的类"-->
        <aop:aspect ref="diy">
            <!--切入点-->
            <aop:pointcut id="pointcut123" expression="execution(* service.UserServiceImpl.*(..))"/>
            <!--通知--><!--pointcut-ref="待切入点"-->
            <aop:before method="before" pointcut-ref="pointcut123"/>
            <aop:after method="after" pointcut-ref="pointcut123"/>
        </aop:aspect>
    </aop:config>
    ~~~

- 其余同方法一

#### 使用注解实现

- 想切入的（切面）

    ~~~java
    import org.aspectj.lang.annotation.*; 	// 包不要导错
    @Aspect     // 标注这个类是一个切面
    public class AnnotationPointCut {
        @Before("execution(* service.UserServiceImpl.*(..))")
        public void before(){
            System.out.println("方法执行前...");
        }
        @After("execution(* service.UserServiceImpl.*(..))")
        public void after(){
            System.out.println("方法执行后...");
        }
    }
    ~~~

- 注册bean，实现aop

    ~~~xml
    <!--方式三：使用注解-->
    <bean id="xxx" class="div.AnnotationPointCut"/>
    <!--开启注解支持-->
    <aop:aspectj-autoproxy/>
    ~~~

- 使用注解实现类似自定义

**常用的注解：**

- @Before		 在指定方法运行之前执行
- @After            在指定方法运行之前执行
- @Around
- @AfterThrowing

## 整合Mybatis

[mybatis-spring –](http://mybatis.org/spring/zh/index.html)

1. 导入相关的jar包

    - junit
    - mybatis
    - mysql数据库
    - spring相关的
    - aspectjweaver
    - mybatis-spring

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
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.7</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.3.15</version>
        </dependency>
        <!--Spring 操作数据库-->
        <!-- https://mvnrepository.com/artifact/org.springframework/spring-jdbc -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.3.15</version>
        </dependency>
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.9.6</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis-spring -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>2.0.7</version>
        </dependency>
    </dependencies>
    ~~~

- 编写mybatis接口的xml

    ~~~xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE mapper
            PUBLIC "-//mybatis.org//DTD mapper 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="dao.UserDao">
        <resultMap id="t" type="pojo.User">
            <result property="pwd" column="password"/>
        </resultMap>
        <select id="selectUser"  resultMap="t">
            select * from user
        </select>
    </mapper>
    ~~~

- 编写数据源配置

    ~~~xml
    <!--DataSource：使用Spring的数据源替换Mybatis的配置
            这里使用Spring提供的JDBC
        -->
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.cj.jdbc.Driver" />
        <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useUnicode=true&amp;characterEncoding=utf-8" />
        <property name="username" value="root" />
        <property name="password" value="123456" />
    </bean>
    ~~~

- SqlSessionFactory

    ~~~xml
    <!--SQLSessionFactory-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource" />
        <!--绑定Mybatis配置文件-->
        <property name="configLocation" value="classpath:mybatis.xml" />
        <property name="mapperLocations" value="classpath:dao/UserDao.xml" />
    </bean>
    ~~~

- SqlSessionTemplate

    ~~~xml
    <!--SqlSessionTemplate:就是我们使用的sqlSession-->
    <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate ">
        <!--只能使用构造器注入sqlSessionFactory-->
        <constructor-arg name="sqlSessionFactory" ref="sqlSessionFactory" />
    </bean>
    ~~~

- 给接口加实现类【比Mybatis多的一步】

    ~~~java
    public class UserDaoImpl implements UserDao {
    
        // 之前所有操作，使用SqlSession. 现在使用SqlSessionTemplate
        private SqlSessionTemplate sqlSession;
    
        public void setSqlSession(SqlSessionTemplate sqlSession) {
            this.sqlSession = sqlSession;
        }
    
        @Override
        public List<User> selectUser() {
            UserDao mapper = sqlSession.getMapper(UserDao.class);
            return mapper.selectUser();
        }
    }
    ~~~

- 将实现类注入到Spring中

    ~~~xml
    <!--注入bean-->
    <bean id="user" class="dao.UserDaoImpl">
        <property name="sqlSession" ref="sqlSession" />
    </bean>
    ~~~

- 测试

    ~~~java
    public void test() throws IOException {
        ApplicationContext context = new ClassPathXmlApplicationContext("spring-dao.xml");
        UserDao user = (UserDao) context.getBean("user");
        List<User> users = user.selectUser();
        for (User user1 : users) {
            System.out.println(user1);
        }
    }
    ~~~

**方法二：** 不使用SqlSessionTemplate，使用SqlSessionDaoSupport

- 省略SqlSessionTemplate的配置，其他一致

- 给接口加实体类

    ~~~java
    public class UserDaoImpl2 extends SqlSessionDaoSupport implements UserDao{
        @Override
        public List<User> selectUser() {
            return getSqlSession().getMapper(UserDao.class).selectUser();
        }
    }
    ~~~

- 将实现类注入到Spring中

    ~~~xml
    <bean id="user2" class="dao.UserDaoImpl2">
        <property name="sqlSessionFactory" ref="sqlSessionFactory"/>
    </bean>
    ~~~

## 事务管理

事务ACID原则：

- 原子性
    - 要么都成功，要么都失败
- 一致性
    - 比如转账，执行前后总金额不能改变，前后一致
- 隔离性
    - 多个事务可能操作同一资源，相互隔离
- 持久性
    - 事务一旦提交，结果不能被影响，被持久化写到存储器中

#### 声明式事务

- 声明式事务：AOP
- 编程式事务：需要在代码中，进行事务的管理

**准备工作**

- 接口

    ~~~java
    public interface UserDao {
        public int insertUser(User  user);
        public int deleteUser(int id);
        public void test1();
    }
    ~~~

- 实现类

    ~~~java
    public class UserDaoImpl extends SqlSessionDaoSupport implements UserDao {
        @Override
        public int insertUser(User user) {
            return getSqlSession().getMapper(UserDao.class).insertUser(user);
        }
        @Override
        public int deleteUser(int id) {
            return getSqlSession().getMapper(UserDao.class).deleteUser(id);
        }
        public void test1(){
            User user = new User(5,"小王","123");
            UserDao mapper = getSqlSession().getMapper(UserDao.class);
            mapper.insertUser(user);
            mapper.deleteUser(1);
        }
    }
    ~~~

- mybatis 的xml配置

    ~~~xml
    <insert id="insertUser" parameterType="pojo.User">
        insert into user(id,name,password) values(#{id},#{name},#{pwd})
    </insert>
    
    <delete id="deleteUser">
        delet from user where id = #{id}
    </delete>
    ~~~

- spring整合mybatis

**事务的配置**

- 需要添加 事务和AOP的约束

    ~~~xml
    xmlns:tx="http://www.springframework.org/schema/tx" 
    xmlns:aop="http://www.springframework.org/schema/aop"
    http://www.springframework.org/schema/tx
    http://www.springframework.org/schema/tx/spring-tx.xsd
    http://www.springframework.org/schema/aop
    https://www.springframework.org/schema/aop/spring-aop.xsd
    ~~~

- 事务的具体配置

    ~~~xml
    <!--配置声明式事务,官网的，必须要有-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource" />
    </bean>
    <!--结合AOP实现事务的织入-->
    <!--配置事务的通知：-->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <!--给哪些方法配置事务-->
        <!--配置事务的传播特性:propagation
            REQUIRED:支持当前事务，如果没有当前事务就新建一个事务
            -->
        <tx:attributes>
            <tx:method name="test1" propagation="REQUIRED"/>
        </tx:attributes>
    </tx:advice>
    <!--配置事务切入-->
    <aop:config>
        <aop:pointcut id="txPointCut" expression="execution(* dao.*.*(..))"/>
        <aop:advisor advice-ref="txAdvice" pointcut-ref="txPointCut"/>
    </aop:config>
    ~~~

- 测试

    ~~~java
    ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
    UserDao userDao = context.getBean("user", UserDao.class);
    userDao.test1();
    ~~~

#### Spring中事务的传播性（Propagation）

- **REQUIRED**：支持当前事务，如果当前没有事务，就新建一个事务。这是最常见的选择。 

- **SUPPORTS**：支持当前事务，如果当前没有事务，就以非事务方式执行。 

- **MANDATORY**：支持当前事务，如果当前没有事务，就抛出异常。 

- **REQUIRES_NEW**：新建事务，如果当前存在事务，把当前事务挂起。 

- **NOT_SUPPORTED**：以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。 

- **NEVER**：以非事务方式执行，如果当前存在事务，则抛出异常。 

- **NESTED**：支持当前事务，如果当前事务存在，则执行一个嵌套事务，如果当前没有事务，就新建一个事务。

一种有七种，基本上只用 REQUIRED
