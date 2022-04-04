## SpringSecurity

注: 导入之后，先把依赖注掉，测一下其他功能正不正常。不注掉依赖，启动项目会一直跳转到一个登录界面

> 是针对Spring项目的安全框架，也是SpringBoot底层安全模块默认的技术选型，可以实现强大的Web安全控制

**几个重要的类：**

- WebSecurityConfigurerAdapter：自定义Security策略
- AuthenticationManagerBuilder：自定义认证策略
- @EnableWebSecurity：开启WebSecurity模式

SpringSecurity的两个主要目标 "认证" 和  "授权" (访问控制)

"认证" （Authentication）

"授权"（Authorization）

>  概念是通用的，不仅仅在SpringSecurity中存在

**导入依赖**

~~~xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
~~~

#### 认证和授权

1. 编写 Security 配置类

参考官网：https://spring.io/projects/spring-security 

对应的帮助文档： [Java Configuration :: Spring Security](https://docs.spring.io/spring-security/reference/servlet/configuration/java.html#jc-httpsecurity)

~~~java
@EnableWebSecurity  // 开启WebSecurity模式
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    // 授权
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()    // 首页所有人可以访问，功能页设置有对应权限的人访问
                .antMatchers("/").permitAll()
                .antMatchers("/level1/**").hasRole("vip1")
                .antMatchers("/level2/**").hasRole("vip2")
                .antMatchers("/level3/**").hasRole("vip3");

        http.formLogin()   // 没有权限默认会到登录页面 // 默认 /authentication/login(官方写好的)
            .loginPage("/toLogin")         // 指定登录页面的跳转路径
            .failureUrl("/test")           // 登录错误的跳转路径
            .loginProcessingUrl("/login"); // 处理登录请求，就是from表单提交的地址
        //                .usernameParameter("username")  // 前端input的用户名的name
        //                .passwordParameter("password") // 前端input的密码的name
    }

    // 认证
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        // 这是在内存中定义的，后面会有数据库的方式
        auth.inMemoryAuthentication().
                .withUser("admin").password("123456").roles("vip2","vip3")		  // admin用户的权限有 vip2、vip3
                .and()
                .withUser("root").password("123456").roles("vip1","vip2","vip3"); // root用户的权限有 vip1、vip2、vip3
    }
}
~~~

可能会报出` There is no PasswordEncoder mapped for the id "null"`  这是密码没有设置编码格式，修改 认证处的代码为

~~~java
// 认证
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    // 这是在内存中定义的，后面会有数据库的方式
    auth.inMemoryAuthentication().passwordEncoder(new BCryptPasswordEncoder())
        .withUser("admin").password(new BCryptPasswordEncoder().encode("123456")).roles("vip2","vip3")
        .and()
        .withUser("root").password(new BCryptPasswordEncoder().encode("123456")).roles("vip1","vip2","vip3");
}
~~~

#### 权限控制和注销

1. 前端

~~~html
<a class="item" th:href="@{/logout}">
    <i class="address card icon"></i> 注销
</a>
~~~

2. 注销功能

~~~java
// 在授权功能中添加
// 授权
@Override
protected void configure(HttpSecurity http) throws Exception {
    http.authorizeRequests()    // 首页所有人可以访问，功能页设置有对应权限的人访问
        .antMatchers("/").permitAll()
        .antMatchers("/level1/**").hasRole("vip1")
        .antMatchers("/level2/**").hasRole("vip2")
        .antMatchers("/level3/**").hasRole("vip3");
        http.logout()    // 注销.  // The default is that accessing the URL "/logout"
               // .logoutUrl("/logout1")       // 注销跳转的路径
                .logoutSuccessUrl("/");        // 注销成功后跳转的界面
               //.deleteCookies("remove")              // 注销后移除 cookie
               //.invalidateHttpSession(false)         // 改成 true 则 注销后移除 session
}
~~~

注销可能不成功，因为为了防止 *跨站请求伪造* 攻击。需要使用 post 方式提交或者使用

~~~java
http.csrf().disable();
~~~

#### 记住我

~~~java
http.rememberMe();  // 记住我功能 cookie实现，默认保存两周
~~~

## Shiro

> Apache Shiro 是一个 Java的安全（权限）框架
>
> Shiro 可以在JavaSE 和 JavaEE 环境
>
> Shiro 可以完成 认证、授权、加密、会话管理，Web继承，缓存等

官网：[Apache Shiro | Simple. Java. Security.](https://shiro.apache.org/index.html)

**核心三大对象：**

![](./images/springsecurity01.png)

- Subject：当前的用户
- SecurityManager：管理所有用户。本质上是一个应用程序单例，默认的SecurityManager实现是POJO，可以使用任何与POJO兼容的配置机制进行配置，基本上可以使用任何能够实例化类和调用JavaBeans兼容方法的东西。
- Realm：连接数据，是一个特定于安全性的DAO：它封装了数据源的连接细节，并根据需要将相关数据提供给Shiro

#### 整合到springboot

导入依赖

~~~xml
<!-- https://mvnrepository.com/artifact/org.apache.shiro/shiro-spring -->
<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-spring</artifactId>
    <version>1.8.0</version>
</dependency>
~~~

1. 编写Shiro配置

- 先编写一个UserRealme

~~~java
// 自定义的 UserRealm  需要 extends AuthorizingRealm
public class UserRealm extends AuthorizingRealm {
    // 授权
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        System.out.println("===AuthorizationInfo===执行了");
        return null;
    }
    // 认证
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        System.out.println("===AuthenticationInfo===执行了");
        return null;
    }
}
~~~

- ShiroConfig

~~~java
@Configuration
public class ShiroConfig {
    // shiroFilterFactoryBean
    @Bean
    public ShiroFilterFactoryBean getShiroFilterFactoryBean(DefaultWebSecurityManager DefaultWebSecurityManager){
        ShiroFilterFactoryBean shiroFilterFactoryBean = new ShiroFilterFactoryBean();
        // 设置安全管理器
        shiroFilterFactoryBean.setSecurityManager(DefaultWebSecurityManager);
        return shiroFilterFactoryBean;
    }
    // DafaultWebSecurityManager
    @Bean
    public DefaultWebSecurityManager getDefaultWebSecurityManager(UserRealm userRealm){
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
        // 关联UserRealm
        securityManager.setRealm(userRealm);
        return securityManager;
    }
    // 创建 realm 对象，需要自定义类
    @Bean
    public UserRealm userRealm(){
        return new UserRealm();
    }
}
~~~

#### 登陆拦截

- 测试的controller

~~~java
@Controller
public class MyController {
    @RequestMapping("/")
    public String toIndex(){ return "index";}
    @RequestMapping("/add")
    public String add(){ return "user/add";}
    @RequestMapping("/update")
    public String update(){ return "user/update";}
    @RequestMapping("/toLogin")
    public String login(){ return "login";}
}
~~~

ShiroConfig的配置

~~~java
@Bean
public ShiroFilterFactoryBean getShiroFilterFactoryBean(DefaultWebSecurityManager DefaultWebSecurityManager){
    ShiroFilterFactoryBean shiroFilterFactoryBean = new ShiroFilterFactoryBean();
    // 设置安全管理器
    shiroFilterFactoryBean.setSecurityManager(DefaultWebSecurityManager);
    /*
            anon：无需认证就可以访问
            authc：必须认证了才能访问
            user：必须拥有记住我功能才能用
            perms：拥有对某个资源的权限才能访问
            role：拥有某个角色权限才能访问
         */
    // 添加shiro的内置过滤器
    Map<String, String> filerMap = new HashMap<>();
    filerMap.put("/add","authc");
    shiroFilterFactoryBean.setFilterChainDefinitionMap(filerMap);
    // 设置登录的请求
    shiroFilterFactoryBean.setLoginUrl("/toLogin");
    return shiroFilterFactoryBean;
}
~~~

