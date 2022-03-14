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

## CRUD Controller和视图层编写

#### 查

- index.jsp 编写

    ~~~jsp
    <%@ page contentType="text/html;charset=UTF-8" language="java" %>
    <html>
        <head>
            <title>$Title$</title>
        </head>
        <body>
            <a href="${pageContext.request.contextPath}/allBook">进入图书列表界面</a>
        </body>
    </html>
    ~~~

- 书籍列表页面 allBook.jsp

    ~~~jsp
    <body>
        <table>
            <thead>
                <tr>
                    <th>书籍编号</th>
                    <th>书籍名字</th>
                    <th>书籍数量</th>
                    <th>书籍详情</th>
                    <th>操作</th>
                </tr>
            </thead>
    
            <tbody>
                <c:forEach var="book" items="${requestScope.get('list')}">
                    <tr>
                        <td>${book.getBookID()}</td>
                        <td>${book.getBookName()}</td>
                        <td>${book.getBookCounts()}</td>
                        <td>${book.getDetail()}</td>
                        <td>
                            <a href="${pageContext.request.contextPath}/toUpdate?id=${book.getBookID()}">更改</a> |
                            <a href="${pageContext.request.contextPath}/del/${book.getBookID()}">删除</a>
                        </td>
                    </tr>
                </c:forEach>
            </tbody>
        </table>
    </body>
    ~~~

- Controller 层编写

    ~~~java
    @Controller
    public class BookController {
        // controller 调用 service 层
        @Autowired
        private BookService bookService;
    
        // 查询全部的书籍
        @RequestMapping("/allBook")
        public String list(Model model){
            List<Books> list = bookService.queryAllBook();
            System.out.println(list);
            model.addAttribute("list",list);
            return "allBook";
        }
    }
    ~~~

#### 加

- 编写添加界面 addBook.jsp

    ~~~jsp
    <body>
        <form action="${pageContext.request.contextPath}/addBook" method="post">
            书籍名称：<input type="text" name="bookName"><br><br><br>
            书籍数量：<input type="text" name="bookCounts"><br><br><br>
            书籍详情：<input type="text" name="detail"><br><br><br>
            <input type="submit" value="添加">
        </form>
    </body>
    ~~~

- Controller 层编写

    ~~~java
    // 添加书籍    
    // 跳转到添加书籍界面
    @RequestMapping("/toAddBook")
    public String toAddPage(){
        return "addBook";	  // 要是用后台过去，因为目录在WEB-INF下
    }
    @RequestMapping("/addBook")
    public String addBook(Books books){
        bookService.addBook(books);
        return "redirect:/allBook";  // 此处要用重定向，不然会过不去
    }
    ~~~

#### 改

- 更新书籍界面 updateBook.jsp

    ~~~jsp
    <form action="${pageContext.request.contextPath}/updateBook" method="post">
        <input type="hidden" name="bookID" value="${book.getBookID()}"/>
        书籍名称：<input type="text" name="bookName" value="${book.getBookName()}"/>
        书籍数量：<input type="text" name="bookCounts" value="${book.getBookCounts()}"/>
        书籍详情：<input type="text" name="detail" value="${book.getDetail() }"/>
        <input type="submit" value="提交"/>
    </form>
    ~~~

- Controller 层编写

    ~~~java
    // 更改书籍
    // 跳转到更改界面
    @RequestMapping("/toUpdate")
    public String toUpdateBook(Model model,int id){
        Books book = bookService.queryBookById(id);
        model.addAttribute("book",book);
        return "updateBook";
    }
    // 更改书籍
    @RequestMapping("/updateBook")
    public String updateBook(Books books){
        bookService.updateBook(books);
        return "redirect:/allBook";  // 此处要用重定向，不然会过不去
    }
    ~~~

#### 删

- Controller 层编写

    ~~~java
    // 删除书籍
    @RequestMapping("/del/{id}")
    public String deleteBook(@PathVariable("id") int id){   //RestFul风格
        bookService.delBookById(id);
        return "redirect:/allBook";  // 此处要用重定向，不然会过不去
    }
    ~~~

## Ajax 登录提示

- 编写 login.jsp 

    ~~~jsp
    <%@ page contentType="text/html;charset=UTF-8" language="java" %>
    <html>
        <head>
            <title>Title</title>
            <script src="static/jquery-2.0.0.min.js"></script>
            <script>
                function a1(){
                    $.ajax({
                        url:"${pageContext.request.contextPath}/aa1",   // 请求的地址
                        data:{'name':$("#name").val()},     // 向后端传送的数据
                        success:function (data){            // 回调函数，后端返回的数据
                            if (data.toString()==='ok'){
                                $("#userInfo").css("color","green");
                            }else{
                                $("#userInfo").css("color","red");
                            }
                            $("#userInfo").html(data)
                        }
                    })
                }
                function a2(){
                    $.ajax({
                        url:"${pageContext.request.contextPath}/aa1",   // 请求的地址
                        data:{'pwd':$("#pwd").val()},     // 向后端传送的数据
                        success:function (data){            // 回调函数，后端返回的数据
                            if (data.toString()==='ok'){
                                $("#pwdInfo").css("color","green");
                            }else{
                                $("#pwdInfo").css("color","red");
                            }
                            $("#pwdInfo").html(data)
                        }
                    })
                }
            </script>
        </head>
        <body>
            <p>用户名：<input type="text" id="name" onblur="a1()">
                <span id="userInfo"></span>
            </p>
            <p>密码：<input type="password" id="pwd" onblur="a2()">
                <span id="pwdInfo"></span>
            </p>
        </body>
    </html>
    ~~~

- spring-mvc.xml json乱码过滤

    ~~~xml
    <!--开启mvc注解驱动,并解决Json乱码-->
    <mvc:annotation-driven>
        <mvc:message-converters register-defaults="true">
            <bean class="org.springframework.http.converter.StringHttpMessageConverter">
                <constructor-arg value="UTF-8"/>
            </bean>
            <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
                <property name="objectMapper">
                    <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
                        <property name="failOnEmptyBeans" value="false"/>
                    </bean>
                </property>
            </bean>
        </mvc:message-converters>
    </mvc:annotation-driven>
    ~~~

- Controller 层编写

    ~~~java
    // 登录Ajax
    @RequestMapping( value = "/aa1")
    @ResponseBody   // 使下面方法不走SpringMVC视图解析器
    public String logTest(String name,String pwd){
        String msg = "";
        if (name != null){
            if ("admin".equals(name)){
                msg = "ok";
            }else{
                msg = "用户名有误";
            }
        }
        if (pwd != null){
            if ("123456".equals(pwd)){
                msg = "ok";
            }else{
                msg = "密码有误";
            }
        }
        return msg;
    }
    ~~~

## 用户登录拦截

- index.jsp

    ~~~jsp
    <h1><a href="${pageContext.request.contextPath}/goLogin">登录页面</a></h1>
    <h1><a href="${pageContext.request.contextPath}/main">首页</a></h1>
    ~~~

- login.jsp

    ~~~jsp
    <h1>登录页面</h1>
    <form action="${pageContext.request.contextPath}/login" method="post">
        用户：<input type="text" name="username">
        密码：<input type="text" name="password">
        <input type="submit" value="提交">
    </form>
    ~~~

- main.jsp

    ~~~jsp
    <h1>首页</h1>
    <h3>${userLoginInfo}</h3>
    <h2><a href="/outLogin">注销</a></h2>
    ~~~

- 登录 Controller

    ~~~java
    @Controller
    public class LoginController {
        @RequestMapping("/main")
        public String main(){
            return "main";
        }
        @RequestMapping("/goLogin")
        public String goLogin(){
            return "login";
        }
        @RequestMapping("/login")
        public String login(String username, String password, HttpSession session){
            // 将用户信息存在session中
            session.setAttribute("userLoginInfo",username);
            return "main";
        }
        // 注销
        @RequestMapping("/outLogin")
        public String out(HttpSession session){
            session.removeAttribute("userLoginInfo");
            System.out.println("zhuxiao le ");
            return "redirect:/index.jsp";
        }
    }
    ~~~

- 拦截器

    ~~~java
    public class LoginInterceptor implements HandlerInterceptor {
        @Override
        public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
            if (request.getRequestURI().contains("goLogin")){
                return true;
            }
            if (request.getRequestURI().contains("login")){
                return true;
            }
            if (request.getSession().getAttribute("userLoginInfo")!=null){
                return true;
            }
            request.getRequestDispatcher("/WEB-INF/jsp/login.jsp").forward(request,response);
            return false;
        }
    }
    ~~~

- 配置拦截器，使其生效

    ~~~xml
    <!--拦截器配置-->
    <mvc:interceptors>
        <mvc:interceptor>
            <!-- /** 代表拦截所有请求 -->
            <mvc:mapping path="/**"/>
            <bean class="config.LoginInterceptor"/>
        </mvc:interceptor>
    </mvc:interceptors>
    ~~~

## 文件上传与下载

需要用到两个包 ：

Commons-io  [Maven :commons-io](https://mvnrepository.com/artifact/commons-io/commons-io)

commons-fileupload  [Maven :commons-fileupload ](https://mvnrepository.com/artifact/commons-fileupload/commons-fileupload)

在HTML页面input必须有**`name <input="file" name="filename">`**

**表单如果包含一个文件上传输入项的话，这个表单的 enctype 属性就必须设置为`enctype="multipart/form-data"`** 因为这样设置才会以流的形式传输

- Spring MVC为文件上传提供了直接的支持，是MultipartResolver实现的。

#### 上传

- 配置springxxxx.xml **这个bena的id必须为：multipartResolver** 

    ~~~xml
    <!--文件上传配置-->
    <bean id="multipartResolver"  class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
        <!-- 请求的编码格式，必须和jSP的pageEncoding属性一致，以便正确读取表单的内容，默认为ISO-8859-1 -->
        <property name="defaultEncoding" value="utf-8"/>
        <!-- 上传文件大小上限，单位为字节（10485760=10M） -->
        <property name="maxUploadSize" value="10485760"/>
        <property name="maxInMemorySize" value="40960"/>
    </bean>
    ~~~

- 前端界面

    ~~~JSP
    <form action="${pageContext.request.contextPath}/upload2" enctype="multipart/form-data" method="post"/>
    <input type="file"  name="file">
    <input type="submit" value="upload">
    </form>
    ~~~

- Controller层      **方法一**

    ~~~java
    @Controller
    public class FileController {
        //@RequestParam("file") 将name=file控件得到的文件封装成CommonsMultipartFile 对象
        //批量上传CommonsMultipartFile则为数组即可
        @RequestMapping("/upload")
        public String fileUpload(@RequestParam("file") CommonsMultipartFile file , HttpServletRequest request) throws IOException {
    
            //获取文件名 : file.getOriginalFilename();
            String uploadFileName = file.getOriginalFilename();
    
            //如果文件名为空，直接回到首页！
            if ("".equals(uploadFileName)){
                return "redirect:/index.jsp";
            }
            System.out.println("上传文件名 : "+uploadFileName);
    
            //上传路径保存设置
            String path = request.getSession().getServletContext().getRealPath("/upload");
            //如果路径不存在，创建一个
            File realPath = new File(path);
            if (!realPath.exists()){
                realPath.mkdir();
            }
            System.out.println("上传文件保存地址："+realPath);
    
            InputStream is = file.getInputStream(); //文件输入流
            OutputStream os = new FileOutputStream(new File(realPath,uploadFileName)); //文件输出流
    
            //读取写出
            int len=0;
            byte[] buffer = new byte[1024];
            while ((len=is.read(buffer))!=-1){
                os.write(buffer,0,len);
                os.flush();
            }
            os.close();
            is.close();
            return "redirect:/index.jsp";
        }
    }
    ~~~

- Controller    **方法二**    **采用file.Transto 来保存上传的文件**

    ~~~java
    /*
    * 采用file.Transto 来保存上传的文件
    */
    @RequestMapping("/upload2")
    public String  fileUpload2(@RequestParam("file") CommonsMultipartFile file, HttpServletRequest request) throws IOException {
    
        //上传路径保存设置
        String path = request.getSession().getServletContext().getRealPath("/upload");
        File realPath = new File(path);
        if (!realPath.exists()){
            realPath.mkdir();
        }
        //上传文件地址
        System.out.println("上传文件保存地址2："+realPath);
    
        //通过CommonsMultipartFile的方法直接写文件（注意这个时候）
        file.transferTo(new File(realPath +"/"+ file.getOriginalFilename()));
    
        return "redirect:/index.jsp";
    }
    ~~~

#### 下载

- 前端

    ~~~JSP
    <a href="/download">点击下载</a>
    ~~~

- Controller 

    ~~~java
    @RequestMapping(value="/download")
    public String downloads(HttpServletResponse response , HttpServletRequest request) throws Exception{
        //要下载的图片地址
        String  path = request.getSession().getServletContext().getRealPath("/static");
        String  fileName = "测试.png";
    
        //1、设置response 响应头
        response.reset(); //设置页面不缓存,清空buffer
        response.setCharacterEncoding("UTF-8"); //字符编码
        response.setContentType("multipart/form-data"); //二进制传输数据
        //设置响应头
        response.setHeader("Content-Disposition",
                           "attachment;fileName="+ URLEncoder.encode(fileName, "UTF-8"));
    
        File file = new File(path,fileName);
        //2、 读取文件--输入流
        InputStream input=new FileInputStream(file);
        //3、 写出文件--输出流
        OutputStream out = response.getOutputStream();
    
        byte[] buff =new byte[1024];
        int index=0;
        //4、执行 写出操作
        while((index= input.read(buff))!= -1){
            out.write(buff, 0, index);
            out.flush();
        }
        out.close();
        input.close();
        return null;
    }
    ~~~

