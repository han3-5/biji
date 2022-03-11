## HTTP

#### 请求

- 客户端 --> 发请求（Request）--> 服务器
- get 和 post 区别
    - get请求携带的参数比较**少**，大小**有**限制。url栏中会显示数据内容，**不安全**，但效率**高**
    - post请求携带的参数比较**多**，大小**无**限制。url栏中**不会**显示数据内容，**安全**，但效率**低**

> 百度举例

~~~java
Request URL: https://www.baidu.com/		// 请求地址
Request Method: GET						// get/post 方法
Status Code: 200 OK						// 状态码
Remote Address: 180.101.49.11:443		// 地址
~~~

~~~java
Accept: text/html
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9
Cache-Control: max-age=0
Connection: keep-alive
~~~

#### 响应

- 服务器  --> 响应（Response） --> 客户端

> 百度举例

~~~java
Cache-Control: private					// 缓存控制
Connection: keep-alive					// 连接后保持连接
Content-Encoding: gzip					// 编码
Content-Type: text/html;charset=utf-8	// 类型
~~~

## Servlet

Servlet（Server Applet）是服务连接器，用Java编写的服务器端程序，具有生成动态Web内容的功能，Servlet是指Java语言实现的一个接口，指任何实现了这个Servlet接口的类。Servlet可以响应任何类型的请求

#### 运行机制

浏览器发送一个HTTP请求，HTTP请求由Web容器（例如Tomcat服务器）接收，服务器根据URL执行指定的Servlet，Servlet处理请求然后响应数据

## HelloServlet

- 编写一个Servlet程序
    - 编写一个普通类
    - 实现Servlet接口，这里继承HttpServlet

~~~java
public class HelloServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.setContentType("text/html");	// 设置编码类型
        resp.setCharacterEncoding("utf-8");	// 设置编码格式
        System.out.println("进入deGet方法");
        resp.getWriter().println("hello,Serlcet!");
    }
}
~~~

- 编写Servlet 映射

> 我们写的是JAVA程序，但是需要通过浏览器访问，而浏览器需要连接web服务器，所以在web服务器中注册我们写的Servlet，还需要給浏览器一个能够访问的路径

~~~xml
<!--注册Servlet-->
<servlet>
    <servlet-name>ni</servlet-name>
    <servlet-class>HelloServlet</servlet-class>
</servlet>
<!--Servlet请求路径-->
<servlet-mapping>
    <!--和上面的name保持一致-->
    <servlet-name>ni</servlet-name>
    <!--项目目录/hello-->
    <url-pattern>/hello</url-pattern>
</servlet-mapping>
~~~

> 会设置默认请求路径，不会直接出首页了

~~~xml
<servlet-mapping>
    <servlet-name>ni</servlet-name>
    <!--将不会去访问index.xxx 而是直接使用servlet-->
    <url-pattern>/*</url-pattern>
</servlet-mapping>
~~~

> 使用 通配符.后缀名的时候不能加上 "/"

~~~xml
<servlet-mapping>
    <servlet-name>ni</servlet-name>
    <!--不能加上 "/" -->
    <url-pattern>*.txt</url-pattern>
</servlet-mapping>
~~~

- 配置tomcat。需要注意配置发布的路径

#### 优先级问题

指定了固有的映射路径优先级最高，如果找不到会走默认的处理请求

~~~xml
<servlet-mapping>
    <!--优先级比下面的那个高-->
    <servlet-name>ni</servlet-name>
    <url-pattern>/hello</url-pattern>
</servlet-mapping>
<servlet-mapping>
    <servlet-name>error</servlet-name>
    <url-pattern>/*</url-pattern>
</servlet-mapping>
~~~

## ServletContext对象

web容器在启动的时候，它会为每个web程序都创建一个对应的ServletContext对象，**它代表了当前的web应用**

#### 共享数据

> 之后会被session代替

在这个Servlet中保存的数据，可以在另一个Servlet中拿到

> 设置数据

~~~java
// 设置数据
public class Servlet01 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletContext servletContext = this.getServletContext();
        String username = "zyh你好";    //数据
        // 讲一个值保存在servletContext中。名字叫username。值是username
        servletContext.setAttribute("username",username);
    }
}
~~~

~~~xml
<servlet>
    <servlet-name>set</servlet-name>
    <servlet-class>Servlet01</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>set</servlet-name>
    <url-pattern>/test01</url-pattern>
</servlet-mapping>
~~~

> 获取数据

~~~java
// 获取数据
public class Servlet02 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletContext servletContext = this.getServletContext();
        resp.setContentType("text/html");	// 设置编码类型
        resp.setCharacterEncoding("utf-8");	// 设置编码格式
        resp.getWriter().println(servletContext.getAttribute("username"));
    }
}
~~~

~~~xml
<servlet>
    <servlet-name>get</servlet-name>
    <servlet-class>Servlet02</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>get</servlet-name>
    <url-pattern>/test02</url-pattern>
</servlet-mapping>
~~~

#### 获取初始化参数

~~~xml
<!--配置一些web应用初始化参数-->
    <context-param>
        <param-name>url</param-name>
        <param-value>urlnihao</param-value>
    </context-param>
~~~

~~~java
ServletContext servletContext = this.getServletContext();
servletContext.getInitParameter("url");
~~~

#### 请求转发

> 请求转发和重定向的区别：请求转发，url不会变。重定向 url会改变

~~~java
// 类名 xx
ServletContext servletContext = this.getServletContext();
// forward(req,resp)	刷新
servletContext.getRequestDispatcher("/t2").forward(req,resp);
// 也就是我访问的是 /t1, 但是页面上显示的是 /t2 的内容
// "/t2"的"/"指的是项目根路径
~~~

~~~XML
<servlet>
    <servlet-name>t</servlet-name>
    <servlet-class>xx</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>t</servlet-name>
    <url-pattern>/t1</url-pattern>
</servlet-mapping>
~~~

#### 读取资源文件

Properties

- java 和 resources 都被打包到了classes 目录下
- 思路：可以使用文件流

~~~properties
username = root
password = 123456
~~~

~~~java
public class Servlet04 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        InputStream inputStream = this.getServletContext().getResourceAsStream("/WEB-INF/classes/test.properties");
        Properties properties = new Properties();
        properties.load(inputStream);
        String user = properties.getProperty("username");
        String password = properties.getProperty("password");
        resp.getWriter().println(user);
        resp.getWriter().println(password);
    }
}
~~~

~~~xml
<servlet>
    <servlet-name>t4</servlet-name>
    <servlet-class>Servlet04</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>t4</servlet-name>
    <url-pattern>/test04</url-pattern>
</servlet-mapping>
~~~

## HttpServletRequest

web服务器接收到客户端的http请求，针对这个请求，分别创建一个代表请求的HttpServletRequest对象。一个HttpServletResponse

**如果要获取客户端请求过来的参数：使用 HttpServletRequest**

#### 获取前端传递的参数

~~~java
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    req.setCharacterEncoding("utf-8");				// 设置请求的编码
    resp.setCharacterEncoding("utf-8");				// 设置返回的编码
    String username = req.getParameter("username");	// 获取到前端的信息
    String password = req.getParameter("password"); // 获取到前端的信息
    System.out.println(username);
    System.out.println(password);
}
~~~

## HttpServletResponse

web服务器接收到客户端的http请求，针对这个请求，分别创建一个代表请求的HttpServletRequest对象。一个HttpServletResponse

**如果要给客户端响应一些信息：使用HttpServletResponse**

- 负责向浏览器发送数据的方法

~~~java
ServletOutputStream getOutputStream() throws IOException;
PrintWriter getWriter() throws IOException;
~~~

- 负责向浏览器发送响应头的方法

~~~java
void setCharacterEncoding(String var1);

void setContentLength(int var1);

void setContentType(String var1);

void setDateHeader(String var1, long var2);

void addDateHeader(String var1, long var2);

void setHeader(String var1, String var2);

void addHeader(String var1, String var2);

void setIntHeader(String var1, int var2);

void addIntHeader(String var1, int var2);
~~~

#### 下载文件

1. 获取下载文件的路径
2. 获取下载的文件名
3. 让浏览器支持下载
4. 获取下载文件的输出流，I/O 流操作

~~~java
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    // 1. 获取下载文件的路径
    String filePath = "D:\\shangke\\chengxu\\mavenTest\\src\\main\\resources\\5.jpg";
    // 2. 获取下载的文件名
    String fileName = filePath.substring(filePath.lastIndexOf("\\")+1);
    // 3. 让浏览器支持下载		URLEncoder.encode()设置编码
    resp.setHeader("Content-Disposition","attachment;filename="+URLEncoder.encode(filename,"utf-8"));
    // 4. 获取下载文件的输出流 I/O 操作
    FileInputStream in = new FileInputStream(filePath);
    ServletOutputStream out = resp.getOutputStream();   // 创建服务器响应的资源输出流
    byte[] buffer = new byte[1024];
    int len = 0;
    while((len = in.read(buffer)) != -1){
        out.write(buffer,0,len);
    }
    out.flush();
    out.close();
    in.close();
}
~~~

#### 验证码

#### 重定向

> 重定向的状态码是 302

~~~java
resp.sendRedirect("/../..");	//路径要写全
~~~

## 关于WEB-INF一些

正常访问WEB-INF目录下的文件是访问不到的，用户不可见。

**但是可以通过请求转发做到**

## cookie

- 客户端技术（数据在客户端）

~~~java
// 常用类，方法
Cookie[] cookies = req.getCookies();	// 获取cookies
cookie.getName();						// 获得cookie中的key
cookie.getValue();						// 获得cookie中的value		
Cookie cookie = new Cookie("name", "an你好");	// 新建一个cookie
cookie.setMaxAge(60*60);				// 设置cookie 的有效期
resp.addCookie(cookie);					// 响应一个cookie
~~~

~~~java
resp.setContentType("text/html");	// 设置编码类型
req.setCharacterEncoding("utf-8");
resp.setCharacterEncoding("utf-8");
// Cookie，这是服务器从客户端获取的cookie，是个数组，说明cookie不止一个
Cookie[] cookies = req.getCookies();
PrintWriter out = resp.getWriter();
// 用来判断cookie 是否存在
if (cookies != null){
    out.write("上一次访问的时间是");
    for (int i = 0; i < cookies.length; i++) {
        Cookie cookie = cookies[i];
        if (cookie.getName().equals("name")){
            out.write(cookie.getValue());
        }
    }
}else {
    out.write("这是你第一次访问");
}
// 給客户端一个 cookie
Cookie cookie = new Cookie("name", "an你好");
resp.addCookie(cookie);
//设置cookie存在时间
cookie.setMaxAge(60*60);
~~~

## *session

- 服务器技术，可以保存用户的信息
- 服务器会给每个用户创建一个session（一个浏览器就算一个用户）

~~~java
// 常用类，方法
getId()					// 获得唯一的标识符
getServletContext()		// 获得整个Servelet的上下文
getAttribute()			// 得到一个节点
setAttribute()			// 设置一个节点
removeAttribute()		// 移除一个节点
isNew()					// 是否是新的 session
invalidate()			// 注销(清掉这个session)
~~~

~~~java
req.setCharacterEncoding("utf-8");
resp.setContentType("text/html;charset=utf-8");
resp.setCharacterEncoding("utf-8");
//得到session
HttpSession session = req.getSession();
//給session存东西
session.setAttribute("name","namenihao");
// 获取session的id
String id = session.getId();
// 判断session 是不是新的
if (session.isNew()){
    resp.getWriter().println("创建成功了");
}else{
    resp.getWriter().write("已经存在，session为"+id);
}
~~~

> session是可以传对象的

~~~java
//得到session
HttpSession session = req.getSession();
//給session存东西
session.setAttribute("name",可以是个对象);
~~~

> 推测session创建的时候做了什么

~~~java
// 推测session创建的时候做了什么
Cookie cookie = new Cookie("JSESSIONID",sessionId);
resp.addCookie(cookie);
~~~

#### 注销session

> 通过手动注销

~~~java
session.invalidate();	// xml不要忘记配置
~~~

> web.xml 中配置

~~~xml
<!--设置session默认的失效时间-->
<session-config>
	<!--一天后session自动失效，以分钟为单位-->
    <session-timeout>1440</session-timeout>
</session-config>
~~~

####  session和cookie区别

- cookie是吧用户的数据写给用户的浏览器，浏览器保存，可以保存多个
- sessio把用户的数据写到用户独占session中，服务器端保存
- session对象由服务器创建

**使用场景：**

- 保存一个登录用户的信息
- 购物车信息
- 在整个网站中经常会使用的数据，将其保存在session中
