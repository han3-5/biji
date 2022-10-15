前后端分离时代，通过接口进行交互

## 使用

[API Documentation & Design Tools for Teams | Swagger](https://swagger.io/)

在项目中使用Swagger 需要springbox

- swagger2
- ui

## SpringBoot中集成

#### SpringBoot2.5.6 版本

springfox-swagger2 、 springfox-swagger-ui 不能高过2.9.2

1. 导入依赖

~~~xml
<!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger2 -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.9.2</version>
</dependency>
<!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger-ui -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.9.2</version>
</dependency>
~~~

2. SwaggerConfig

~~~java
@Configuration
@EnableSwagger2 // swageer2.x 开启swagger
public class SwaggerConfig {

}
~~~

3. 测试地址  http://localhost:8080/swagger-ui.html

#### SpringBoot2.6.6 版本

> 就不能像上方那般配置

参考：[Springfox Reference Documentation](https://springfox.github.io/springfox/docs/snapshot/#migrating-from-existing-2-x-version)

1. 导入依赖（此处使用的是swagger3,3只有一个依赖）

~~~xml
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-boot-starter</artifactId>
    <version>3.0.0</version>
</dependency>
~~~

2. SwaggerConfig

~~~java
@Configuration
@EnableOpenApi  // swageer3 之后开启的方式
public class SwaggerConfig {

}
~~~

如此配置后swagger可能报错，需要修改mvc默认的路径匹配策略。修改`application.yml`文件，MVC默认的路径匹配策略为`PATH_PATTERN_PARSER`，需要修改为`ANT_PATH_MATCHER`；

~~~properties
spring.mvc.pathmatch.matching-strategy=ant_path_matcher
~~~

3. 测试  http://localhost:8080/swagger-ui/index.html

#### 使用knife4j

> knife4j 是为java mvc框架继承Swagger生成Api文档的增强解决方案
>
> 有个好处，不用纠结上方版本问题

1. 导入依赖

~~~xml
<dependency>
    <groupId>com.github.xiaoymin</groupId>
    <artifactId>knife4j-spring-boot-starter</artifactId>
    <version>3.0.2</version>
</dependency>
~~~

2. 导入knif4j相关配置类

~~~java
@Configuration
public class SwaggerConfig {
    // 配置了 Swagger 的Docket 的bean实例
    @Bean
    public Docket docket(){
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.zyh.reggie.controller"))
                // .paths()     过滤什么路径
                .paths(PathSelectors.ant("/**"))
                .build();
        // RequestHandlerSelectors 配置要扫描接口的方式
        // basePackage()  指定要扫描的包
        // any()            扫描全部
    }
    // 配置Swagger信息 = apiInfo
    private ApiInfo apiInfo(){
        // 作者信息
        Contact contact = new Contact("", "", "");
        return new ApiInfo(
                "你好呀",
                "Api Documentation",
                "1.0",
                "urn:tos",
                contact,
                "Apache 2.0",
                "http://www.apache.org/licenses/LICENSE-2.0",
                new ArrayList()
        );
    }
}
~~~

3. 设置静态资源，否则接口文档页面无法访问

~~~java
// 设置静态资源映射
@Override
protected void addResourceHandlers(ResourceHandlerRegistry registry) {
    registry.addResourceHandler("doc.html").addResourceLocations("classpath:/META-INF/resources/");
    registry.addResourceHandler("/webjars/**").addResourceLocations("classpath:/META-INF/resources/webjars/");
}
~~~

4. 在LoginCheckFilter中设置不需要处理的请求路径

~~~java
// 就是配置Filter 来放行，不设置也没关系，就是可能需要登录一下
~~~

## 配置

**Swagger的bean实例 Docket**

通过apiInfo 配置Swagger的信息，这个地方进源码看一下需要什么复制出来改成自己想要的样子就可以

~~~java
@Configuration
public class SwaggerConfig {
    // 配置了 Swagger 的Docket 的bean实例
    @Bean
    public Docket docket(){
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo());
    }
    // 配置Swagger信息 = apiInfo
    private ApiInfo apiInfo(){
        // 作者信息
        Contact contact = new Contact("", "", "");
        return new ApiInfo(
                "你好呀",
                "Api Documentation",
                "1.0",
                "urn:tos",
                contact,
                "Apache 2.0",
                "http://www.apache.org/licenses/LICENSE-2.0",
                new ArrayList()
        );
    }
}
~~~

#### 配置扫描接口

com.example.springbootswagger.swagger.**controller** 如下

~~~java
@Controller
public class HelloController {
    @ResponseBody
    @GetMapping("/hello")
    public String hello(){
        return "hello";
    }
}
~~~

SwaggerConfig

~~~java
// 配置了 Swagger 的Docket 的bean实例
@Bean
public Docket docket(){
    return new Docket(DocumentationType.SWAGGER_2)
        .apiInfo(apiInfo())
        .select()
        // RequestHandlerSelectors 配置要扫描接口的方式
        // basePackage()  指定要扫描的包
        // any()            扫描全部
        // none()           全部不扫描
        // withClassAnnotation(Mapping.class) 扫描类上的注解
        // withMethodAnnotation(Bean.class)   扫描方法上的注解
       .apis(RequestHandlerSelectors.basePackage("com.example.springbootswagger.swagger.controller"))// 扫描的包路径
        // .paths()     过滤什么路径
        .paths(PathSelectors.ant("/**"))
        .build();
}
~~~

#### 配置是否启动

~~~java
// 配置了 Swagger 的Docket 的bean实例
@Bean
public Docket docket(){
    return new Docket(DocumentationType.SWAGGER_2)
        .apiInfo(apiInfo())
        .enable(false)		// 默认为true，改为false后不能再浏览器访问swagger
        .select()
        .apis(RequestHandlerSelectors.basePackage("com.example.springbootswagger.swagger.controller"))
        .paths(PathSelectors.ant("/**"))
        .build();
}
~~~

通过设置生产环境来切换swagger是否能访问

~~~java
public class SwaggerConfig {
    // 配置了 Swagger 的Docket 的bean实例
    @Bean
    public Docket docket(Environment environment){
        // 设置要显示的Swagger环境 
        Profiles profiles = Profiles.of("dev", "test");
        // 通过environmentacceptsProfiles判断是否处在自己设定的环境中
        boolean flag = environment.acceptsProfiles(profiles);

        return new Docket(DocumentationType.SWAGGER_2)
            .apiInfo(apiInfo())
            .enable(flag)
            .select()
            .apis(RequestHandlerSelectors.basePackage("com.example.springbootswagger.swagger.controller"))
            .paths(PathSelectors.ant("/**"))
            .build();
    }
}
~~~

只希望在生产环境使用，发布中不使用

1. 方法一：在配置文件中配置

~~~properties
#spring.profiles.active=dev
springfox.documentation.swagger-ui.enabled=false
~~~

~~~properties
# application-dev.properties 文件
springfox.documentation.swagger-ui.enabled=true
~~~

2. 方法二：

~~~java
//Environment的包是org.springframework.core.env.Environment;
@Bean
public Docket docket(Environment environment){
    // 设置要显示的Swagger环境
    Profiles profiles = Profiles.of("dev", "test");
    // 通过environmentacceptsProfiles判断是否处在自己设定的环境中
    boolean flag = environment.acceptsProfiles(profiles);

    return new Docket(DocumentationType.SWAGGER_2)
        .enable(flag)
        .apiInfo(apiInfo())
        .select()
        .apis(RequestHandlerSelectors.basePackage("com.example.springbootswagger.swagger.controller"))
        .paths(PathSelectors.ant("/**"))
        .build();
}
~~~

#### 分组注释

~~~java
@Bean
public Docket docket(){
    return new Docket(DocumentationType.SWAGGER_2)
        .apiInfo(apiInfo())
        .groupName("zhang")		// 分组
        .select()
        .apis(RequestHandlerSelectors.basePackage("com.example.springbootswagger.swagger.controller"))
        .paths(PathSelectors.ant("/**"))
        .build();
}
~~~

分多个组，各人管理各人的

~~~java
@Bean
public Docket docket1(){return new Docket(DocumentationType.SWAGGER_2).groupName("A");}
@Bean
public Docket docket2(){return new Docket(DocumentationType.SWAGGER_2).groupName("B");}
@Bean
public Docket docket3(){return new Docket(DocumentationType.SWAGGER_2).groupName("C");}
~~~

#### 注解

@Api 都是为了加注释

> @Api(tags = "xxx模块说明")					作用在模块类上
>
> @ApiModel("xxxPOJO说明")
>
> @ApiModelProperty("xxxPOJO类属性的说明")
>
> @ApiOperation("xxx接口说明") 			  给方法加注释
>
> @ApiParam("xxxx参数说明")					给参数加注释
>
> @ApiImplicitParams({})						用在请求方法上，表示一组参数说明
>
> @ApiImplicitParam							  用在@ApiImplicitParams注解中，指定一个请求参数的各个方面

~~~java
@Api(tags = "测试一下")
@RestController
public class HelloController {
    @GetMapping("/hello")
    @ApiOperation("n你好")
    public String hello(@ApiParam("名字") String username){
        return "hello";
    }
    @GetMapping("/page")
    @ApiImplicitParams({
        // required 是否是必须的
            @ApiImplicitParam(name = "page",value = "页码",required = true),
            @ApiImplicitParam(name = "pageSize",value = "每页记录数",required = true),
            @ApiImplicitParam(name = "name",value = "名称",required = false),
    })
    public R<Page> page(int page,int pageSize,String name){
        // 代码...
    }
}
~~~

