

##  微服务

> 微服务是一种架构风格。就像是把一个单独的应用程序开发为一套小服务，每个小服务运行在自己的进程中，并使用轻量级机制通信，通常是 HTTP API。这些服务围绕业务能力来构建，并通过完全自动化部署机制来独立部署。这些服务使用不同的编程语言书写，以及不同数据存储技术，并保持最低限度的集中式管理。

[微服务（Microservices）](https://www.cnblogs.com/liuning8023/p/4493156.html)

## 第一个SpringBoot

[Spring Initializr](https://start.spring.io/) 在线配置

#### 修改springboot启动标

在 `resources` 目录下新建 `banner.txt` 里面放入的springboot就会识别应用

[Ascii艺术字实现个性化Spring Boot启动banner图案，-bootschool.net](https://bootschool.net/ascii-art)

## 原理初探

自动配置：

**pom.xml**

- pom.xml中最上方<parent>xxx<artifactId>spring-boot-starter-parent</artifactId>xxx</parent>保存springboot的父依赖，点进去后的<parent>xxx<artifactId>spring-boot-dependencies</artifactId>xxx</parent>中保存着核心依赖
- 在引入一些springboot依赖的时候，不需要指定版本，就是因为有这些仓库

**启动器：**

启动器也就是springboot的启动场景

~~~xml
<!--在外侧的pom.xml中-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
</dependency>
~~~

- 比如spring-boot-starter-web，就会自动导入web环境所有的依赖
- springboot会将所有的功能场景都变成一个个的启动器

**主程序：**

~~~java
// SpringBootApplication ： 标注这个类是一个 springboot 的应用
@SpringBootApplication
public class BootMybatisApplication {
    public static void main(String[] args) {
        // 将 springboot 应用启动
        SpringApplication.run(BootMybatisApplication.class, args);
    }
}
~~~

- 注解：

    - @SpringBootConfiguration：springboot的配置类，标注在某个类上，表示这是一个SpringBoot的配置类，进去后

        ~~~java
        // 得到spring的配置类
        @Configuration
        
        // 再进入后
        @Component	// 说明是一个spring的组件
        ~~~

    - @EnableAutoConfiguration：开启自动装配
    
        ~~~java
        // 从@EnableAutoConfiguration 进入后
        @AutoConfigurationPackage// 自动配置包
        	// 从@AutoConfigurationPackage进入后
        	@Import({Registrar.class})//Registrar.class 作用：将主启动类的所在包及包下面所有子包里面的所有组件扫描到Spring容器 ；
        @Import({AutoConfigurationImportSelector.class})// 给容器导入组件
        ~~~
    
    - 进入AutoConfigurationImportSelector
    
        ~~~java
        // 类中有这么一个方法
        
        // 获得候选的配置
        protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
            //这里的getSpringFactoriesLoaderFactoryClass（）方法
            //返回的就是我们最开始看的启动自动导入配置文件的注解类；EnableAutoConfiguration
            List<String> configurations = SpringFactoriesLoader.loadFactoryNames(this.getSpringFactoriesLoaderFactoryClass(), this.getBeanClassLoader());
            Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you are using a custom packaging, make sure that file is correct.");
            return configurations;
        }
        ~~~
    
        这个方法又调用了  SpringFactoriesLoader 类的静态方法！我们进入SpringFactoriesLoader类loadFactoryNames() 方法
    
        ~~~java
        
        public static List<String> loadFactoryNames(Class<?> factoryClass, @Nullable ClassLoader classLoader) {
            String factoryClassName = factoryClass.getName();
            //这里它又调用了 loadSpringFactories 方法
            return (List)loadSpringFactories(classLoader).getOrDefault(factoryClassName, Collections.emptyList());
        }
        ~~~
    
        继续点击查看 loadSpringFactories 方法
    
        ~~~java
        private static Map<String, List<String>> loadSpringFactories(@Nullable ClassLoader classLoader) {
            //获得classLoader ， 我们返回可以看到这里得到的就是EnableAutoConfiguration标注的类本身
            MultiValueMap<String, String> result = (MultiValueMap)cache.get(classLoader);
            if (result != null) {
                return result;
            } else {
                try {
                    //去获取一个资源 "META-INF/spring.factories"
                    Enumeration<URL> urls = classLoader != null ? classLoader.getResources("META-INF/spring.factories") : ClassLoader.getSystemResources("META-INF/spring.factories");
                    LinkedMultiValueMap result = new LinkedMultiValueMap();
        
                    //将读取到的资源遍历，封装成为一个Properties
                    while(urls.hasMoreElements()) {
                        URL url = (URL)urls.nextElement();
                        UrlResource resource = new UrlResource(url);
                        Properties properties = PropertiesLoaderUtils.loadProperties(resource);
                        Iterator var6 = properties.entrySet().iterator();
        
                        while(var6.hasNext()) {
                            Entry<?, ?> entry = (Entry)var6.next();
                            String factoryClassName = ((String)entry.getKey()).trim();
                            String[] var9 = StringUtils.commaDelimitedListToStringArray((String)entry.getValue());
                            int var10 = var9.length;
        
                            for(int var11 = 0; var11 < var10; ++var11) {
                                String factoryName = var9[var11];
                                result.add(factoryClassName, factoryName.trim());
                            }
                        }
                    }
        
                    cache.put(classLoader, result);
                    return result;
                } catch (IOException var13) {
                    throw new IllegalArgumentException("Unable to load factories from location [META-INF/spring.factories]", var13);
                }
            }
        }
        ~~~
    
        发现一个多次出现的文件：spring.factories
    
- 在External Libraries下的`org.springframework.boot:spring-boot-autoconfigure` 下。

    打开 spring.factories，有很多的配置文件，这些就是自动配置的根源所在

    点开这些配置类可以看到JavaConfig配置类，而且都注入了一些Bean

所以，自动配置真正实现是从classpath中搜寻所有的META-INF/spring.factories配置文件 ，并将其中对应的 org.springframework.boot.autoconfigure. 包下的配置项，通过反射实例化为对应标注了 @Configuration的JavaConfig形式的IOC容器配置类 ， 然后将这些都汇总成为一个实例并加载到IOC容器中。

**结论：**

1. SpringBoot在启动的时候从类路径下的 META-INF/spring.factories 中获取EnableAutoConfiguration指定的值
2. 这些值作为自动配置类导入容器，自动配置类就生效了

## 主启动类

> SpringApplication，主启动类会直接开启一个服务

~~~java
SpringApplication.run(BootMybatisApplication.class, args);
~~~

SpringApplication.run有两部分组成

- 一部分是SpringApplication的实例化
- 另一部分是run方法的执行

这个类主要做了四件事情：

1. 推断应用的类型是普通的项目还是Web项目
2. 查找并加载所有可用初始化器 ， 设置到initializers属性中
3. 找出所有的应用程序监听器，设置到listeners属性中
4. 推断并设置main方法的定义类，找到运行的主类

构造器：

~~~java

public SpringApplication(ResourceLoader resourceLoader, Class... primarySources) {
    // ......
    this.webApplicationType = WebApplicationType.deduceFromClasspath();
    this.setInitializers(this.getSpringFactoriesInstances();
    this.setListeners(this.getSpringFactoriesInstances(ApplicationListener.class));
    this.mainApplicationClass = this.deduceMainApplicationClass();
}
~~~

**run方法执行流程**

![](./images/springboot-01.jpg)

## yaml

> "YAML Ain't a Markup Language" 	YAML不是一种标记语言
>
> "Yet Another Markup Language"      YAML仍是一种标记语言
>
> yaml 这个语言是以数据作为中心，而不是以标记语言为重点！

SpringBoot使用一个全局的配置文件，配置文件名称是固定的

- application.properties
    - 语法： key=value
- application.yml
    - 语法：key:空格value

传统xml配置

~~~xml
<server>
    <port>8081<port>
</server>
~~~

yaml配置

~~~yaml
server：
  prot: 8080
~~~

**yaml 基本语法**

~~~yaml
# 普通key-value
name: zhang
# 对象
student:
  name: zhang
  age: 18
# 行内写法
student1: {name: zhang,age: 18}
#数组
animal:
  - cat
  - dog
animal1: [cat,dog]
~~~

## 给属性赋值

准备两个测试类

- dog

~~~java
public class Dog {
    private String name;
    private Integer age;
    // 此处省略get、set、toString、有参、无参
}
~~~

- person

~~~java
public class Person {
    private String name;
    private Integer age;
    private Boolean happy;
    private Date birth;
    private Map<String,Object> map;
    private List<Object> list;
    private Dog dog;
    // 此处省略get、set、toString、有参、无参
}
~~~

#### properties方法

1. 在 `resources` 目录下新建 `test.properties` 文件

~~~properties
name=张
age=18
~~~

2. 需要被赋值的类

~~~java
@Component
@PropertySource("classpath:test.properties")    // 加载配置文件
public class Person {
    @Value("${name}")	// 要写成 ${} 来取值
    private String name;
    @Value("${age}")
    private Integer age;
    private Boolean happy;
    private Date birth;
    private Map<String,Object> map;
    private List<Object> list;
    private Dog dog;
}
~~~

3. 测试

~~~java
@SpringBootTest
class BootMybatisApplicationTests {
    @Autowired
    Person person;
    @Test
    void test(){
        System.out.println(person);
    }
}
~~~

如果输出乱码之类的，需要设置properties files文件的编码为UTF-8

打开设置 	---> 	 查找到 `File Encodings` 	--->       设置`Default encoding fro properties files:` UTF-8  ---->   勾选后面的 `Transparent native-to-ascii conversion`

#### yaml 方法

1. 在application.yaml 文件中编写

~~~yaml
person1:
  name: 张
  age: 3
  happy: true
  birth: 2020/1/1
  map: {k1: v1,k2: v2}
  list: ["code","girl"]
  dog:
    name: 旺财
    age: 1
~~~

2. 需要被赋值的类

~~~java
@ConfigurationProperties(prefix = "person1")
public class Person {
    private String name;
    private Integer age;
    private Boolean happy;
    private Date birth;
    private Map<String,Object> map;
    private List<Object> list;
    private Dog dog;
}
~~~

在写 @ConfigurationProperties 注解的时候可能idea会爆红，但点开文档是404，需要改变文档版本

需要在pom.xml 中加入依赖

~~~xml
<!-- 导入配置文件处理器，配置文件进行绑定就会有提示，需要重启 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
~~~

3. 测试

~~~java
@SpringBootTest
class BootMybatisApplicationTests {
    @Autowired
    Person person;
    @Test
    void test(){
        System.out.println(person);
    }
}
~~~

如果想把数据不写在application.yaml中，需要在application.yaml导入即可

~~~yaml
spring:
  config:
    import: classpath:test.yaml
~~~

#### 小结对比

|                    | @ConfigurationProperties | @Value     |
| ------------------ | ------------------------ | ---------- |
| 功能               | 批量注入配置文件中的属性 | 一个个指定 |
| 松散绑定(松散语法) | 支持                     | 不支持     |
| SpEL表达式         | 不支持                   | 支持       |
| JSR303数据校验     | 支持                     | 不支持     |
| 复杂类型封装(对象) | 支持                     | 不支持     |

松散绑定：比如yam中写last-name，类中可以写lastName. `-` 

**官方推荐使用yaml**

- 如果在某个业务中，只需要获取配置文件中的某个值，可以使用一下@value
- 如果编写了一个JavaBean来和配置文件进行映射，就直接使用@ConfigurationProperties

## JSR303校验

引入依赖

~~~xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
~~~

Dog类

~~~java
@Component
@ConfigurationProperties(prefix = "dog")
@Validated  //数据校验
public class Dog {
    private String name;
    @Min(value = 5,message = "最小值不能小于5")
    private Integer age;
}
~~~

application.yaml

~~~yaml
dog:
  name: 旺财
  age: 3
~~~

**常用校验注解：**

~~~java
@Null				// 被注释的元素必须为null
@NotNull			// 被注释的元素必须不为null
@AssertTrue			// 被注释的元素必须为true
@AssertFalse		// 被注释的元素必须为false
@Min(value)			// 被注释的元素必须是一个数字，其值必须大于等于指定的最小值
@Max(value)			// 被注释的元素必须是一个数字，其值必须大于等于指定的最大值
@DecimalMin(value)	// 被注释的元素必须是一个数字，其值必须大于等于指定的最小值
@DecimalMax(value)	// 被注释的元素必须是一个数字，其值必须大于等于指定的最大值
@Size(max,min)		// 被注释的元素大小必须在指定的范围内
@Email				// 被注释的元素必须是电子邮箱地址
@Length				// 被注释的元素大小必须在指定的范围内
@NotEmpty			// 被注释的字符串必须非空
@Pattern			// 验证 String 对象是否符合正则表达式的规则
~~~

## 多环境配置

#### 放的位置

application 这个配置文件可以放的位置及优先级

1. `file:./config/`		  `file` 指的是项目目录
2. `file:./`
3. `classpath:/config`    `classpath` 指的是`/src/main/resources` 目录下
4. `classpath:/`                 此处是系统自动生成的

#### 多环境切换

在主配置文件编写的时候，文件名可以是 application-{xxx}.properties/yaml,用来指定多个环境版本

比如

- application-dev.properties 代表开发环境配置

- application-test.properties 代表测试环境配置

但是Springboot并不会直接启动这些配置文件，它默认会使用application.properties主配置文件启动，需要手动激活：

~~~properties
# application.properties
spring.profiles.active=dev 	# 切换环境
~~~

**yaml的多文档块**

和properties配置文件中一样，但是使用yml去实现不需要创建多个配置文件

~~~yaml
server:
  port: 8081
#选择要激活那个环境块
spring:
  profiles:
    active: prod
---			# 使用 --- 来分割不同配置环境
server:
  port: 8083
spring:
  profiles: dev #配置环境的名称
---
server:
  port: 8084
spring:
  profiles: prod  #配置环境的名称
~~~

**优先级问题：**

`xxx.properties ` 	> 	`xxx.yml`		>		`xxx.yaml`

- 小技巧：

项目打包好以后，我们可以使用命令行参数的形式，启动项目的时候来指定配置文件的新位置；相同配置，外部指定的配置文件优先级最高

~~~bash
java -jar spring-boot-config.jar --spring.config.location=F:/application.properties
~~~

## 自动配置原理

以**HttpEncodingAutoConfiguration**为例

~~~java
@Configuration	// 表示这是一个配置类，和以前编写的配置文件一样，也可以给容器中添加组件；
//启动指定类的ConfigurationProperties功能;
    //进入这个xxxProperties查看，将配置文件中对应的值和xxxProperties绑定起来；
    //并把xxxProperties加入到ioc容器中
@EnableConfigurationProperties({ServerProperties.class})
//Spring底层@Conditional注解 都是在条件判断，如果满足指定的条件，整个配置类里面的配置就会生效
@ConditionalOnWebApplication(	//这里的意思就是判断当前应用是否是web应用，如果是，当前配置类生效
    type = Type.SERVLET
)
@ConditionalOnClass({CharacterEncodingFilter.class})//判断当前项目有没有这个类
@ConditionalOnProperty(//判断配置文件中是否存在某个配置
    prefix = "server.servlet.encoding",
    value = {"enabled"},
    matchIfMissing = true
)
public class HttpEncodingAutoConfiguration {
    //他已经和SpringBoot的配置文件映射了
    private final Encoding properties;
    //只有一个有参构造器的情况下，参数的值就会从容器中拿
    public HttpEncodingAutoConfiguration(HttpProperties properties) {
        this.properties = properties.getEncoding();
    }

    //给容器中添加一个组件，这个组件的某些值需要从properties中获取
    @Bean
    @ConditionalOnMissingBean
    public CharacterEncodingFilter characterEncodingFilter() {
        CharacterEncodingFilter filter = new OrderedCharacterEncodingFilter();
        filter.setEncoding(this.properties.getCharset().name());
        filter.setForceRequestEncoding(this.properties.shouldForce(org.springframework.boot.web.servlet.server.Encoding.Type.REQUEST));
        filter.setForceResponseEncoding(this.properties.shouldForce(org.springframework.boot.web.servlet.server.Encoding.Type.RESPONSE));
        return filter;
    }
~~~

**总结下来就是：根据当前不同的条件判断，决定这个配置类是否生效**

- 一但这个配置类生效；这个配置类就会给容器中添加各种组件；
- 这些组件的属性是从对应的properties类中获取的，这些类里面的每一个属性又是和配置文件绑定的；
- 所有在配置文件中能配置的属性都是在xxxxProperties类中封装着；
- 配置文件能配置什么就可以参照某个功能对应的这个属性类

~~~java
@ConfigurationProperties(
    prefix = "server",
    ignoreUnknownFields = true
)
// .....
~~~

#### 总结

1. SpringBoot启动会加载大量的自动配置类
2. 给容器中自动配置类添加组件的时候，会从properties类中获取某些属性，只需要在配置文件中指定这些属性的值就可以

@xxxAutoConfiguration：自动配置类； 给容器添加组件

@xxxProperties：封装配置文件中相关的属性

~~~properties
# 有一个配置可以查看自动配置的类有没有生效
debug=true
~~~

**Positive matches:**			自动配置启动的（正匹配）

**Negative matches:**		  自动配置没有成功启动的（负匹配）

**Unconditional classes:**	没有条件的类



## Web(springboot)开发

templates 目录下不能直接访问，只能通过 Controller访问

#### 静态资源处理

全局搜索WebMvcAutoConfiguration 这个配置类

其中有一个方法：addResourceHandlers 添加资源处理

~~~java
public void addResourceHandlers(ResourceHandlerRegistry registry) {
    if (!this.resourceProperties.isAddMappings()) {
        logger.debug("Default resource handling disabled");
    } else {
        this.addResourceHandler(registry, "/webjars/**", "classpath:/META-INF/resources/webjars/");
        this.addResourceHandler(registry, this.mvcProperties.getStaticPathPattern(), (registration) -> {
            registration.addResourceLocations(this.resourceProperties.getStaticLocations());
            if (this.servletContext != null) {
                ServletContextResource resource = new ServletContextResource(this.servletContext, "/");
                registration.addResourceLocations(new Resource[]{resource});
            }

        });
    }
}
~~~

阅读源码可以知道：

1. 所有的 /webjars/** ， 都需要去 classpath:/META-INF/resources/webjars/ 找对应的资源；

2. `this.resourceProperties.getStaticLocations()` 点开`resourceProperties`属性，他是`Resources` 类的实例对象，点开` Resources`类中有一个静态数组

    ~~~java
    private static final String[] CLASSPATH_RESOURCE_LOCATIONS = new String[]{"classpath:/META-INF/resources/", "classpath:/resources/", "classpath:/static/", "classpath:/public/"};
    ~~~

    这四个位置就静态资源可以存在的位置

**webjars**

Webjars本质就是以jar包的方式引入我们的静态资源 ， 我们以前要导入一个静态资源文件，直接导入即可。

网站：https://www.webjars.org 

比如要使用jQuery，我们只要要引入jQuery对应版本的**pom依赖**即可！

访问：只要是静态资源，SpringBoot就会去对应的路径寻找资源，我们这里访问：localhost:8080/webjars/jquery/3.4.1/jquery.js

**第二种方式（常用）**

~~~java
classpath:/META-INF/resources/
classpath:/resources/
classpath:/static/
classpath:/public/
~~~

`classpath` 代表`src/main/resources`

优先级 `resources`	> 	`static`	>		`public`

**自定义资源路径**

~~~properties
spring.web.resources.static-locations=classpath:/hello/
~~~

一旦自己定义了静态文件夹的路径，原来的自动配置就都会失效了！

#### thymeleaf模板引擎

> 模板引擎的作用就是我们写一个页面模板，但是有些值是动态的，我们写一些表达式。而这些值，从后台封装一些数据。然后把模板和数据交给模板引擎，模板引擎按照我们这个数据把表达式解析、填充到我们指定的位置

**引入**

1. Thymeleaf 官网：https://www.thymeleaf.org/
2. Thymeleaf 在Github 的主页：https://github.com/thymeleaf/thymeleaf

导入pom依赖

~~~xml
<dependency>
    <groupId>org.thymeleaf</groupId>
    <artifactId>thymeleaf-spring5</artifactId>
</dependency>
<dependency>
    <groupId>org.thymeleaf.extras</groupId>
    <artifactId>thymeleaf-extras-java8time</artifactId>
</dependency>
~~~

**简单测试：**

- Controller

~~~java
@Controller
public class HelloController {
    @RequestMapping("/hello")
    public String hello(Model model){
        model.addAttribute("msg","ttt");
        return "test";
    }
}
~~~

- test.html		thymeleaf取值

~~~html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org" lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h3 th:text="${msg}"></h3>
<h3>[[ ${msg} ]]</h3> 
</body>
</html>
~~~

**基本语法：**

~~~html
<!DOCTYPE html>
<!--加了idea才会去识别，后面的 th:xx 才会提示-->
<html xmlns:th="http://www.thymeleaf.org">
</html>
~~~

[Tutorial: Using Thymeleaf](https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html#standard-expression-syntax)

~~~html
<!--msg:"<h1>ttt</h1>"-->
<h3 th:text="${msg}"></h3>
<h3 th:utext="${msg}"></h3>
~~~

**for**

~~~html
<!--model.addAttribute("msg",list);-->
<h3 th:each="item : ${msg}" th:text="${item}"></h3>
~~~

#### 拦截器

拦截器interceptor：可以使得若干请求都会自动执行其中的代码组件。该组件可以对所处理的请求选择放行，或者 阻止运行。需要实现**HandlerInterceptor**接口

- 拦截器的具体实现

~~~java
public class LoginHandlerInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        Object loginUser = request.getSession().getAttribute("loginUser");
        if (loginUser == null){ // 没有登录
            request.setAttribute("message","没有权限，请先登录");
            request.getRequestDispatcher("/index.html").forward(request,response);
            return false;
        }else {
            return true;
        }
    }
}
~~~

- 配置拦截器到springboot中

~~~java
@Configuration
public class MyMvcConfig implements WebMvcConfigurer {
    //拦截器配置,添加拦截器的配置路径
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        //addPathPatterns 配置拦截路径  黑名单
        //excludePathPatterns 配置除外路径  白名单  保证注册登录可以正常使用 放开首页、静态资源、登录请求
        registry.addInterceptor(new LoginHandlerInterceptor())
                .addPathPatterns("/**").excludePathPatterns("/","/index.html","/user/login","/css/*","/img/*","/js/*");
    }
}
~~~

## 扩展MVC

**需要依赖支持**	**需要导入jar包**

**springboot** 的MVC

~~~java
@RequestMapping("/test11")
public String testt(){
    return "test";	//  templates/index.html
}
~~~

[Spring Boot Reference Documentation](https://docs.spring.io/spring-boot/docs/2.6.6/reference/htmlsingle/#web.servlet.spring-mvc.auto-configuration)

想要添加额外的MVC配置（拦截器、格式化程序、视图控制器和其他功能），那么您可以添加自己的@configuration类，类型为WebMvcConfigurer（实现这个接口）

```java
@Configuration
public class MyMvcConfig implements WebMvcConfigurer {
    // 添加视图层控制
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        // 添加一个视图控制,addViewController("想去的路径").setViewName("真实的路径")
        registry.addViewController("/").setViewName("index");
        registry.addViewController("/index.html").setViewName("index");
        registry.addViewController("/main.html").setViewName("dashboard");
    }
    // ......
}
```

**全面接管SpringMVC**

全面接管即：SpringBoot对SpringMVC的自动配置不需要了，所有都是我们自己去配置！

只需在我们的配置类中要加一个@EnableWebMvc。

## 页面国际化

> 就是页面上的中英文切换

首先保证idea上的file Ending 全部都为 UTF-8 

[狂神说SpringBoot13：页面国际化 ](https://mp.weixin.qq.com/s?__biz=Mzg2NTAzMTExNg%3D%3D&chksm=ce610719f9168e0fd77185b7194f4cdff964f3f4a6a7584e9ff0afd0ddd99f4e89d9ca2e41bf&idx=1&mid=2247483834&scene=21&sn=e28706bf0c3ded1884452adf6630d43b#wechat_redirect)

## DATA

[Spring Data](https://spring.io/projects/spring-data)

#### 整合JDBC

1. 导入包 **JDBC API、Mysql  driver**

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>
```

2.  application.properties 配置数据源

~~~properties
spring.datasource.username=root
spring.datasource.password=123456
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/mybatis?useUnicode=true&characterEncoding=utf8&useSSl=true&serverTimezone=Asia/Shanghai
~~~

3. 测试一下连接有没有成功

~~~java
@SpringBootTest
class SpringBootDataApplicationTests {
    @Autowired
    DataSource dataSource;	// 应用配置的数据
    @Test
    void contextLoads() throws SQLException {
        System.out.println(dataSource.getClass());  // 打印出使用的数据源连接池
    }

}
~~~

默认给我们配置的数据源为 : class com.zaxxer.hikari.HikariDataSource

HikariDataSource 号称 Java WEB 当前速度最快的数据源，相比于传统的 C3P0 、DBCP、Tomcat jdbc 等连接池更加优秀；

 **JDBCTemplate**

1、有了数据源，就可以拿到数据库连接(java.sql.Connection)，有了连接，就可以使用原生的 JDBC 语句来操作数据库；

2、即使不使用第三方第数据库操作框架，如 MyBatis等，Spring 本身也对原生的JDBC 做了轻量级的封装，即JdbcTemplate。

3、数据库操作的所有 CRUD 方法都在 JdbcTemplate 中。

**JdbcTemplate主要提供以下几类方法：**

- execute方法：可以用于执行任何SQL语句，一般用于执行DDL语句；
- update方法及batchUpdate方法：update方法用于执行新增、修改、删除等语句；batchUpdate方法用于执行批处理相关语句；
- query方法及queryForXXX方法：用于执行查询相关语句；
- call方法：用于执行存储过程、函数相关语句。

**增删改查操作**

~~~java
@SpringBootTest
class SpringBootDataApplicationTests {
    @Autowired
    DataSource dataSource;
    @Autowired
    JdbcTemplate jdbcTemplate;
    // 查询
    @Test
    void test(){
        String sql = "select * from user";
        List<Map<String, Object>> maps = jdbcTemplate.queryForList(sql);
        System.out.println(maps);
    }
    // 新增
    @Test
    void test1(){
        String sql = "insert into user(id,name,password) values(?,?,?)";
        Object[] objects = new Object[3];
        objects[0] = 4;
        objects[1] = "张三";
        objects[2] = 123456;
        jdbcTemplate.update(sql, objects);
    }
    // 更新
    @Test
    void test2(){
        String sql = "update user set name = ? where id = ?";
        Object[] objects = new Object[2];
        objects[0] = "a11";
        objects[1] = 1;
        jdbcTemplate.update(sql, objects);
    }
    // 删除
    @Test
    void test3(){
        String sql = "delete from user where id = 4";
        jdbcTemplate.update(sql);
    }
}

~~~

#### 整合Mybatis

1. 导包	**Mybatis、Mysql  driver**

~~~xml
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.2.2</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>
~~~

2. application.properties 配置数据源

~~~properties
spring.datasource.username=root
spring.datasource.password=123456
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/mybatis?useUnicode=true&characterEncoding=utf8&useSSl=true&serverTimezone=Asia/Shanghai
~~~

3. 测试一下连接有没有成功

~~~bash
# 和整合JDBC代码一致
~~~

**增删改查操作**

1. 编写实体类

~~~java
public class User {
    private Integer id;
    private String name;
    private String password;
    // 省略set、get...
}
~~~

2. 编写Mapper

~~~java
@SpringBootApplication
// 表示这是一个 mybatis 的 mapper 类(第二种方法)
// 在主启动类上扫描
@MapperScan("com.example.springbootdata.mapper")
public class SpringBootDataApplication {.....}
~~~

~~~java
@Mapper // 加上这个注解表示这是一个 mybatis 的 mapper 类(第一种方法)
@Repository
public interface UserMapper {
    List<User> queryUserList();
    User queryUserById(int id);
    int addUser(User user);
    int updateUser(User user);
    int deleteUserById(int id);
}
~~~

3. 编写xxx.xml 配置文件

~~~xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.springbootdata.mapper.UserMapper">

    <select id="queryUserList" resultType="User">
        select * from user
    </select>
</mapper>
~~~

4. application.properties 配置文件

> mybatis-plus 不用配置。或者将文件放在mapper接口同一个文件内（但会遇到maven导入静态资源的问题，需要在pom.xml中配置一下）

~~~properties
# 类型别名
mybatis.type-aliases-package=com.example.springbootdata.pojo
# 配置xml文件的路径，不然springboot找不到xml文件
mybatis.mapper-locations=classpath:mapper/*.xml
# 驼峰转换
mybatis.configuration.map-underscore-to-camel-case=true
~~~

5. 测试

~~~java
@SpringBootTest
public class mybatisTest {
    @Autowired
    DataSource dataSource;
    @Autowired
    UserMapper userMapper;
    @Test
    void test(){
        List<User> users = userMapper.queryUserList();
        for (User user : users) {
            System.out.println(user);
        }
    }
}
~~~

**也可以使用注解方式**

2. 编写Mapper

~~~java
@Mapper // 加上这个注解表示这是一个 mybatis 的 mapper 类(第一种方法)
@Repository
public interface UserMapper {
    @Select("select * from user")
    List<User> queryUserList();
}
~~~

3. 测试

~~~java
// 测试代码同上
~~~

**查看执行的sql语句**

> 如果需要让mybatis底层输出的日志 显示在控制台，在application.properties中配置：规定哪些类中的什么级别的日志信息输出在springboot控制台：

~~~properties
logging.level.com.zyh.mapper=trace
~~~

## springboot 默认日志

~~~java
//   SLF4J日志记录器 
// @Slf4j 或者在类上使用注解
private static Logger log = LoggerFactory.getLogger(SpringBootStudyApplicationTests.class);
~~~

**修改日志的默认输出级别**

~~~properties
# 这句代码的意思是将com.zyh.mapper包下的所有类的日志级别都设置为trace级别
logging.level.com.zyh.mapper=trace


#指定某个包下的日志级别
logging.level.com.staticzz=trace
#指定log文件名
#logging.file=SpringBoot.log
#指定log输出路径,如果不指定,默认输出到控制台
logging.path=C:/Users/socra
#指定控制台输出格式
#logging.pattern.console=
#指定文件输出格式
logging.pattern.file=%d{-yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-51level %logger{50} - %msg%n
~~~

