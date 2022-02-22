## JavaBean

实体类

- 必须要有一个无参构造
- 属性必须私有化
- 必须有对应的 get/post 方法

一般用来和数据库的字段做映射 ORM

ORM （object relational  mapping）

## MVC三层架构

MVC ： Model View Controller   模型视图控制器

#### Model

- 业务处理：业务逻辑（Service）
- 数据持久层：CRUD（Dao）

#### View

- 展示数据
- 提供链接发起Servlet请求

#### Controller

- 接受用户的请求：（request：请求参数、Session信息······）
- 交给业务层处理对应的代码
- 控制视图的跳转

~~~java
// 举例
登录--->接收到用户的登录请求--->处理用户的请求(获取用户登录的参数，username，password····)--->交给业务层处理登录业务(判断用户名密码是否正确以及一些事务)--->Dao层查询用户名和密码--->在数据库中查询
~~~

## Filter 过滤器

> Filter 在 javax.servlet.*包下

**Filter 是个接口，要实现这个接口，并重写对应的方法**

~~~java
// 初始化：web服务器启动的时候，就初始化了，随时等待顾虑对象的出现
@Override
public void init(FilterConfig filterConfig) throws ServletException {
    System.out.println("初始化了···");
}
// Chain   链
@Override
public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
    request.setCharacterEncoding("utf-8");
    response.setCharacterEncoding("utf-8");
    response.setContentType("text/html;charset=UTF-8");

    System.out.println("执行前.....");
    chain.doFilter(request, response);  // 让请求继续走，不写，程序到这会被拦截
    System.out.println("执行后.....");
}
//销毁，web服务器关闭的时候，才会销毁
@Override
public void destroy() {
    System.out.println("销毁了···");
}
~~~

在 web.xml 中进行配置

~~~xml
<filter>
    <filter-name>testfilter</filter-name>
    <filter-class>FilterTest</filter-class>
</filter>
<filter-mapping>
    <filter-name>testfilter</filter-name>
    <!--只要是/test.jsp的请求，就会经过这个过滤器-->
    <url-pattern>/test.jsp</url-pattern>
</filter-mapping>
~~~

## 监听器

