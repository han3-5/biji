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

#### 常见应用 -- 登录

用户登录之后才能进入主页，用户注销后就不能进入主页了

> success成功界面	   	error错误界面			login登录界面

~~~jsp
// 登录界面
<form action="/servlet/login" method="get">
    <input type="text" name="username">
    <input type="submit" >
</form>
~~~

~~~jsp
// 成功界面
<h1>主页</h1>
<a href="/servlet/logout">注销</a>
~~~

~~~jsp
// 错误界面
<h1>错误</h1>
<a href="/login.jsp">返回登录界面</a>
~~~

> 登录的判断，成功了跳转成功界面，失败跳转错误界面

~~~java
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    String username = req.getParameter("username"); // 获取前端的参数
    if (username.equals("admin")){      // 正确进入正确网页
        req.getSession().setAttribute(CanShu.UserSession,req.getSession().getId());
        resp.sendRedirect("/sys/success.jsp");
    }else{
        resp.sendRedirect("/error.jsp");    // 错误就重定向到错误界面
    }
}
~~~

~~~xml
<servlet>
    <servlet-name>aa</servlet-name>
    <servlet-class>LoginServlet</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>aa</servlet-name>
    <url-pattern>/servlet/login</url-pattern>
</servlet-mapping>
~~~

> 注销界面，点击注销，将用户session中证明自己的值移除(不要删除session，服务器如果经常创建删除session 会造成压力)

~~~java
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    Object userSession = req.getSession().getAttribute(CanShu.UserSession);
    if (userSession != null){   // 如果有session，说明登录成功了
        req.getSession().removeAttribute(CanShu.UserSession);   // 移除session中那个值
        resp.sendRedirect("/login.jsp");
    }else {
        resp.sendRedirect("/login.jsp");
    }
}
~~~

~~~xml
<servlet>
    <servlet-name>a1</servlet-name>
    <servlet-class>LoginOut</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>a1</servlet-name>
    <url-pattern>/servlet/logout</url-pattern>
</servlet-mapping>
~~~

> 过滤器，如果用户点击注销，则不可以通过复制链接的方式进入成功界面

~~~java
public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
    // ServletRequest 没有那些方法要向下强转成 HttpServletRequest
    HttpServletRequest req = (HttpServletRequest) request;
    HttpServletResponse resp = (HttpServletResponse) response;

    if (req.getSession().getAttribute(CanShu.UserSession) == null){
        resp.sendRedirect("/error.jsp");
    }

    chain.doFilter(request,response);
}
~~~

~~~xml
<filter>
    <filter-name>f1</filter-name>
    <filter-class>LoginFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>f1</filter-name>
    <url-pattern>/sys/*</url-pattern>
</filter-mapping>
~~~

## 监听器

~~~java
// 统计在线人数：统计session
public class ListenerTest implements HttpSessionListener {
    @Override
    public void sessionCreated(HttpSessionEvent se) {
        ServletContext servletContext = se.getSession().getServletContext();
        System.out.println(se.getSession().getId());
        Integer count = (Integer) servletContext.getAttribute("count");
        if (count == null){
            count = 1;
        }else{
            count = count + 1;
        }
        servletContext.setAttribute("count",count);
    }

    // 销毁session
    // 一旦销毁Session就会触发一次这个事件
    @Override
    public void sessionDestroyed(HttpSessionEvent se) {
        ServletContext servletContext = se.getSession().getServletContext();
        // 注销session
        se.getSession().invalidate();
        Integer count = (Integer) servletContext.getAttribute("count");
        if (count == null){
            count = 0;
        }else{
            count = count - 1;
        }
        servletContext.setAttribute("count",count);
    }
}
~~~

~~~xml
<!--注册监听器-->
<listener>
    <listener-class>ListenerTest</listener-class>
</listener>
<session-config>
    <!--一分钟后session自动失效，以分钟为单位-->
    <session-timeout>1</session-timeout>
</session-config>
~~~

## 拦截器和过滤器

过滤器和拦截器的区别如下：

1. 拦截器是基于java的反射机制的，而过滤器是基于函数回调。
2. 拦截器不依赖于servlet容器，过滤器依赖与servlet容器。
3. 拦截器只能对action请求起作用，而过滤器则可以对几乎所有的请求起作用。
4. 拦截器可以访问action上下文、值栈里的对象，而过滤器不能访问。
5. 在action的生命周期中，拦截器可以多次被调用，而过滤器只能在容器初始化时被调用一次。
6. 拦截器可以获取IOC容器中的各个bean，而过滤器就不行，这点很重要，在拦截器里注入一个service，可以调用业务逻辑。
