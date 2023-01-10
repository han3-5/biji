##  SpringSecurity

注: 导入之后，先把依赖注掉，测一下其他功能正不正常。不注掉依赖，启动项目会一直跳转到一个登录界面

> 是针对Spring项目的安全框架，也是SpringBoot底层安全模块默认的技术选型，可以实现强大的Web安全控制

**几个重要的类：**

- WebSecurityConfigurerAdapter：自定义Security策略
- AuthenticationManagerBuilder：自定义认证策略
- @EnableWebSecurity：开启WebSecurity模式

SpringSecurity的两个主要目标 "认证" 和  "授权" (访问控制)

"认证" （Authentication）：验证当前访问系统的是不是本系统用户，无需确认具体是哪个用户

"授权"（Authorization）：经过认证后判断当前用户是否有权限进行某些操作

**导入依赖**

~~~xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
~~~

项目启动后默认用户名是 **user** 密码会输出到控制台

- 登录
  1. 自定义登录接口：调用ProviderManager的方法进行认证，如果认证通过生成JWT。吧用户信息存入redis中
  2. 自定义UserDetailsService：在这个实现中去查询数据库
- 校验
  1. 定义JWT认证过滤器 - 获取token、解析token、从redis中获取用户信息、存入SecurityContextHolder

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
        auth.inMemoryAuthentication()
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

#### 使用-认证

1. 重写UserDetailsServiceImpl

~~~java
@Service
public class UserDetailsServiceImpl implements UserDetailsService {
    @Autowired
    private UserMapper userMapper;
    @Autowired
    private MenuMapper menuMapper;
    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        //查询用户信息
        LambdaQueryWrapper<User> queryWrapper = new LambdaQueryWrapper<>();
        queryWrapper.eq(User::getUserName,username);
        User user = userMapper.selectOne(queryWrapper);
        //如果没有查询到用户就抛出异常
        if(Objects.isNull(user)){
            throw new RuntimeException("用户名或者密码错误");
        }
        //        List<String> list = new ArrayList<>(Arrays.asList("test","admin"));
        List<String> list = menuMapper.selectPermsByUserId(user.getId());
        //把数据封装成UserDetails返回
        return new LoginUser(user,list);
    }
}
~~~

2. LoginUser

~~~java
@Data
@NoArgsConstructor
public class LoginUser implements UserDetails {
    private User user;
    private List<String> permissions;
    public LoginUser(User user, List<String> permissions) {
        this.user = user;
        this.permissions = permissions;
    }
    @JSONField(serialize = false)
    private List<SimpleGrantedAuthority> authorities;
    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        if(authorities!=null){
            return authorities;
        }
        //把permissions中String类型的权限信息封装成SimpleGrantedAuthority对象
        //       authorities = new ArrayList<>();
        //        for (String permission : permissions) {
        //            SimpleGrantedAuthority authority = new SimpleGrantedAuthority(permission);
        //            authorities.add(authority);
        //        }
        authorities = permissions.stream()
            .map(SimpleGrantedAuthority::new)
            .collect(Collectors.toList());
        return authorities;
    }
    @Override
    public String getPassword() {
        return user.getPassword();
    }
    @Override
    public String getUsername() {
        return user.getUserName();
    }
    @Override
    public boolean isAccountNonExpired() {
        return true;
    }
    @Override
    public boolean isAccountNonLocked() {
        return true;
    }
    @Override
    public boolean isCredentialsNonExpired() {
        return true;
    }
    @Override
    public boolean isEnabled() {
        return true;
    }
}
~~~

1. controller层
2. service层方法 - controller层调用的方法

~~~java
@Service
public class LoginServiceImpl implements LoginServcie {

    @Autowired
    private AuthenticationManager authenticationManager;

    @Autowired
    private RedisCache redisCache;

    @Override
    public ResponseResult login(User user) {
        //AuthenticationManager authenticate进行用户认证
        UsernamePasswordAuthenticationToken authenticationToken = new UsernamePasswordAuthenticationToken(user.getUserName(),user.getPassword());
        Authentication authenticate = authenticationManager.authenticate(authenticationToken);
        //如果认证没通过，给出对应的提示
        if(Objects.isNull(authenticate)){
            throw new RuntimeException("登录失败");
        }
        //如果认证通过了，使用userid生成一个jwt jwt存入ResponseResult返回
        LoginUser loginUser = (LoginUser) authenticate.getPrincipal();
        String userid = loginUser.getUser().getId().toString();
        String jwt = JwtUtil.createJWT(userid);
        Map<String,String> map = new HashMap<>();
        map.put("token",jwt);
        //把完整的用户信息存入redis  userid作为key
        redisCache.setCacheObject("login:"+userid,loginUser);
        return new ResponseResult(200,"登录成功",map);
    }
}
~~~

3. 配置Security

~~~java
@Configuration
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    //创建BCryptPasswordEncoder注入容器
    @Bean
    public PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }
    @Autowired
    private JwtAuthenticationTokenFilter jwtAuthenticationTokenFilter;
    @Autowired
    private AuthenticationEntryPoint authenticationEntryPoint;
    @Autowired
    private AccessDeniedHandler accessDeniedHandler;
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            //关闭csrf
            .csrf().disable()
            //不通过Session获取SecurityContext
            .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
            .and()
            .authorizeRequests()
            // 对于登录接口 允许匿名访问 - 不匿名就不能访问
            .antMatchers("/user/login").anonymous()
           // .antMatchers("/user/login").permitAll() 无论是否登录都可以访问
            // .antMatchers("/testCors").hasAuthority("system:dept:list222") 授权代替了注解@PreAuthorize("hasAnyAuthority('system:dept:list222')")
            // 除上面外的所有请求全部需要鉴权认证
            .anyRequest().authenticated();
        
        //添加过滤器
        http.addFilterBefore(jwtAuthenticationTokenFilter, UsernamePasswordAuthenticationFilter.class);
        //配置异常处理器
        http.exceptionHandling()
            //配置认证失败处理器
            .authenticationEntryPoint(authenticationEntryPoint)
            .accessDeniedHandler(accessDeniedHandler);
        //允许跨域
        http.cors();
    }
    @Bean
    @Override
    public AuthenticationManager authenticationManagerBean() throws Exception {
        return super.authenticationManagerBean();
    }
}
~~~

4. 认证过滤器

~~~java
@Component
public class JwtAuthenticationTokenFilter extends OncePerRequestFilter {
    @Autowired
    private RedisCache redisCache;
    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
        //获取token
        String token = request.getHeader("token");
        if (!StringUtils.hasText(token)) {
            //放行
            filterChain.doFilter(request, response);
            return;
        }
        //解析token
        String userid;
        try {
            Claims claims = JwtUtil.parseJWT(token);
            userid = claims.getSubject();
        } catch (Exception e) {
            e.printStackTrace();
            throw new RuntimeException("token非法");
        }
        //从redis中获取用户信息
        String redisKey = "login:" + userid;
        LoginUser loginUser = redisCache.getCacheObject(redisKey);
        if(Objects.isNull(loginUser)){
            throw new RuntimeException("用户未登录");
        }
        //存入SecurityContextHolder
        //TODO 获取权限信息封装到Authentication中
        UsernamePasswordAuthenticationToken authenticationToken =
            new UsernamePasswordAuthenticationToken(loginUser,null,loginUser.getAuthorities());
        SecurityContextHolder.getContext().setAuthentication(authenticationToken);
        //放行
        filterChain.doFilter(request, response);
    }
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

#### 注销

~~~java
@Service
public class LoginServiceImpl implements LoginServcie {
    @Autowired
    private AuthenticationManager authenticationManager;
    @Autowired
    private RedisCache redisCache;
    @Override
    public ResponseResult logout() {
        //获取SecurityContextHolder中的用户id
        UsernamePasswordAuthenticationToken authentication = (UsernamePasswordAuthenticationToken) SecurityContextHolder.getContext().getAuthentication();
        LoginUser loginUser = (LoginUser) authentication.getPrincipal();
        Long userid = loginUser.getUser().getId();
        //删除redis中的值
        redisCache.deleteObject("login:"+userid);
        return new ResponseResult(200,"注销成功");
    }
}

~~~



#### 记住我

~~~java
http.rememberMe();  // 记住我功能 cookie实现，默认保存两周
~~~

#### 授权

> 在SpringSecurity中，会使用默认的FilterSecurityInterceptor来进行权限校验。
>
> 在FilterSecurityInterceptor中会从SecurityContextHolder获取其中的Authentication，然后获取其中的权限信息

SpringSecurity提供了注解的权限方案。使用之前需要在配置类上添加

~~~java
@EnableGlobalMethodSecurity(prePostEnabled = true)
~~~

然后就可以使用对应的注解 `@PreAuthorize`

~~~java
@RequestMapping("/hello")
@PreAuthorize("hasAnyAuthority('admin','test','system:dept:list')")
public String hello(){
    public String hello(){
        return "hello";
    }
~~~

**自定义权限校验方法**

1. 创建类SGExpressionRoot

~~~java
@Component("test")
public class SGExpressionRoot {
    public boolean hasAuthority(String authority){
        //获取当前用户的权限
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();
        LoginUser loginUser = (LoginUser) authentication.getPrincipal();
        List<String> permissions = loginUser.getPermissions();
        //判断用户权限集合中是否存在authority
        return permissions.contains(authority);
    }
}
~~~

2. 对应的controller方法上使用注解

~~~java
@RequestMapping("/hello")
@PreAuthorize("@test.hasAuthority('system:dept:list')")
public String hello(){
    return "hello";
}
~~~

#### 自定义失败处理

> 在SpringSecurity中，如果认证或授权的过程中异常会被ExceptionTranslationFilter捕获到。
>
> **认证** 过程中异常会被封装成AuthenticationException然后调用**AuthenticationEntryPoint** 对象进行异常处理
>
> **授权** 过程中异常会被封装成AccessDeniedException然后调用**AccessDeniedHandler** 对象进行异常处理

WebUtils

~~~java
public class WebUtils
{
    /**
     * 将字符串渲染到客户端
     * 
     * @param response 渲染对象
     * @param string 待渲染的字符串
     * @return null
     */
    public static String renderString(HttpServletResponse response, String string) {
        try
        {
            response.setStatus(200);
            response.setContentType("application/json");
            response.setCharacterEncoding("utf-8");
            response.getWriter().print(string);
        }
        catch (IOException e)
        {
            e.printStackTrace();
        }
        return null;
    }
}
~~~

**认证**

~~~java
@Component
public class AuthenticationEntryPointImpl implements AuthenticationEntryPoint {
    @Override
    public void commence(HttpServletRequest request, HttpServletResponse response, AuthenticationException authException) throws IOException, ServletException {
        ResponseResult result = new ResponseResult(HttpStatus.UNAUTHORIZED.value(),"用户认证失败请查询登录");
        String json = JSON.toJSONString(result);
        //处理异常
        WebUtils.renderString(response,json);
    }
}
~~~

**授权**

~~~java
@Component
public class AccessDeniedHandlerImpl implements AccessDeniedHandler {
    @Override
    public void handle(HttpServletRequest request, HttpServletResponse response, AccessDeniedException accessDeniedException) throws IOException, ServletException {
        ResponseResult result = new ResponseResult(HttpStatus.FORBIDDEN.value(),"您的权限不足");
        String json = JSON.toJSONString(result);
        //处理异常
        WebUtils.renderString(response,json);
    }
}

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

[10 Minute Tutorial on Apache Shiro | Apache Shiro](https://shiro.apache.org/10-minute-tutorial.html) 

~~~java
Subject currentUser = SecurityUtils.getSubject();	// 获取当前的用户对象Subject

Session session = currentUser.getSession();			// 通过当前用户拿到session
session.setAttribute( "someKey", "aValue" );		// 存值
String value = (String) session.getAttribute("someKey");// 取值

// 判断当前的用户是否被认证
if ( !currentUser.isAuthenticated() ) {
    UsernamePasswordToken token = new UsernamePasswordToken("lonestarr", "vespa");
    token.setRememberMe(true);			// 设置记住我
    try {
        currentUser.login( token );		// 执行登录操作
    } catch ( UnknownAccountException uae ) {
        // 未知的账户
    } catch ( IncorrectCredentialsException ice ) {
        // 密码不对
    } catch ( LockedAccountException lae ) {
        // 用户名被锁定了
    }
    ... more types exceptions to check if you want ...
        } catch ( AuthenticationException ae ) {
    //unexpected condition - error? 意外错误
}
}

currentUser.getPrincipal()					// 用户的主要信息
currentUser.hasRole( "schwartz" ) 			// 用户的角色
currentUser.isPermitted( "lightsaber:wield" )// 拥有的权限
currentUser.isPermitted( "winnebago:drive:eagle5" )// 拥有的权限
    
currentUser.logout(); 	// 注销
~~~

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
    // DefaultWebSecurityManager
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
    @RequestMapping("/user/add")
    public String add(){ return "user/add";}
    @RequestMapping("/user/update")
    public String update(){ return "user/update";}
    @RequestMapping("/toLogin")
    public String login(){ return "login";}
    
    @RequestMapping("/login1")
    public String login(String username,String password,Model model){
        // 获取当前的用户
        Subject subject = SecurityUtils.getSubject();
        // 封装用户的登录信息
        UsernamePasswordToken token = new UsernamePasswordToken(username, password);

        try {
            subject.login(token);   // 执行登录的方法，如果没有异常，说明 ok
            return "index";
        } catch (UnknownAccountException e) {
            model.addAttribute("msg","用户名错误");
            return "login";
        }catch (IncorrectCredentialsException e){
            model.addAttribute("msg","密码错误");
            return "login";
        }
    }
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
            logout: 退出过滤器，配置一个url就可以使用
         */
    // 添加shiro的内置过滤器
    Map<String, String> filerMap = new HashMap<>();
    // 拦截
    filerMap.put("/user/*","authc");
    shiroFilterFactoryBean.setFilterChainDefinitionMap(filerMap);
    // 设置登录的请求
    shiroFilterFactoryBean.setLoginUrl("/toLogin");
    return shiroFilterFactoryBean;
}
~~~

#### 用户认证

- Controller 层接收这些参数，并进行判断

> 应该在service层，这里比较简单，写在Controller

~~~java
@RequestMapping("/login1")
public String login(String username,String password,Model model){
    // 获取当前的用户
    Subject subject = SecurityUtils.getSubject();
    // 封装用户的登录信息
    UsernamePasswordToken token = new UsernamePasswordToken(username, password);

    try {
        subject.login(token);   // 执行登录的方法，如果没有异常，说明 ok
        return "index";
    } catch (UnknownAccountException e) {
        model.addAttribute("msg","用户名错误");
        return "login";
    }catch (IncorrectCredentialsException e){
        model.addAttribute("msg","密码错误");
        return "login";
    }
}
~~~

- Shiro 认证

~~~java
// 认证
@Override
protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
    System.out.println("===AuthenticationInfo===执行了");
    // 用户名、密码
    String username = "root";
    String password = "123123";
    UsernamePasswordToken userToken = (UsernamePasswordToken) authenticationToken;
    if (!userToken.getUsername().equals(username)){
        return null;    // 会自动抛出异常 UnknownAccountException
    }
    // 密码认证，shiro做
    return new SimpleAuthenticationInfo("",password,"");
}
~~~

#### 整合Mybatis

Mapper

~~~java
@Mapper
public interface UserMapper {
    User queryUserByName(String name);
}
~~~

~~~java
@Autowired
UserMapper UserMapper;
// 认证
@Override
protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
    System.out.println("===AuthenticationInfo===执行了");
    UsernamePasswordToken userToken = (UsernamePasswordToken) authenticationToken;
    // 连接真实的数据库
    User user = userService.queryUserByName(userToken.getUsername());
    if (user == null){	// 没有这个人
        return null;	// 会自动抛出异常 UnknownAccountException
    }
    // 密码认证，shiro做
    // SimpleAuthenticationInfo(将当前用户放到subject,获取当前用户的密码,用户名字)
    return new SimpleAuthenticationInfo(user,user.getPassword(),"");
}
~~~

#### 用户授权

MyController

~~~java
@RequestMapping("/noauth")
@ResponseBody
public String unauth(){
    return "未经授权";
}
~~~

UserRealm

~~~java
// 授权
@Override
protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
    System.out.println("===授权===执行了");
    SimpleAuthorizationInfo simpleAuthorizationInfo = new SimpleAuthorizationInfo();
    //        simpleAuthorizationInfo.addStringPermission("user:add");
    // 拿到当前登录的这个对象
    Subject subject = SecurityUtils.getSubject();
    User currentUser = (User) subject.getPrincipal(); // 拿到User对象
    // 设置当前用户的权限
    simpleAuthorizationInfo.addStringPermission(currentUser.getPerms());
    return simpleAuthorizationInfo;
}
~~~

shiroConfig

~~~java
// 添加shiro的内置过滤器
// 拦截
Map<String, String> filerMap = new HashMap<>();
// 授权   没有授权会跳到未授权的页面
filerMap.put("/user/add","perms[user:add]");
filerMap.put("/user/update","perms[user:update]");

filerMap.put("/user/*","authc");    // 未经授权就访问 authc
//filerMap.put("/logout1","logout");   // 注销功能。要放在最后
shiroFilterFactoryBean.setFilterChainDefinitionMap(filerMap);
// 设置登录的请求
shiroFilterFactoryBean.setLoginUrl("/toLogin");
// 设置未授权的页面
shiroFilterFactoryBean.setUnauthorizedUrl("/noauth");

return shiroFilterFactoryBean;
~~~

#### 用户注销

html 中

~~~html
<a href="/logout1">logout</a>
~~~

- 方法一 在 ShiroConfig中配置 

~~~java
Map<String, String> filerMap = new HashMap<>();
// 授权 
filerMap.put("/user/add","perms[user:add]");
filerMap.put("/user/update","perms[user:update]");
filerMap.put("/user/*","authc");

//filerMap.put("/logout1","logout");   // 注销功能。要放在最后
shiroFilterFactoryBean.setFilterChainDefinitionMap(filerMap);
~~~

- 方法二 在Controller中配置

~~~java
@RequestMapping("/logout1")
@ResponseBody
public String logout1(){
    Subject subject = SecurityUtils.getSubject();
    if (subject.getPrincipal() != null) {
        subject.logout();
        return "退出成功";
    }
    return "没有可以退出的用户";
}
~~~

