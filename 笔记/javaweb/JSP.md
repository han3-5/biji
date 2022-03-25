**了解就好，已经被淘汰了，因为耦合度太高，维护困难**

## JSP

> JavaServer Pages：Java服务器端页面，和Servlet一样，用于动态web技术

最大的特点：

- 写JSP就像是在写HTML
- 区别：
    - HTML 只给用户提供静态的数据
    - JSP页面中可以嵌入JAVA代码，为用户提供动态数据
- 本质上就是一个Servlet
    - 之前手写的Servlet  request.xxx() 全被sun公司集成了

## 生成的代码保存位置

> C:\Users\zyh\AppData\Local\JetBrains\IntelliJIdea2021.2\tomcat\项目名字\work\Catalina\localhost\ROOT\org\apache\jsp

## 基础语法

~~~jsp
<%= 变量或者表达式%>
<!--等价于 EL 表达式-->  ${/代码...}
<%
	// 多行代码
%>
~~~

> x <%= 变量或者表达式%>	${/代码...} 区别

~~~java
<%= 变量%>			// 没有会返回 null
${/代码...}			// 没有就不显示
~~~

> 会被编译到 JSP的类中，其他的会被编译到_jspService 方法中

~~~jsp
<%!
  	// Java代码
%>
~~~

**注释**

> JSP 的注释，不会在客户端显示，HTML的会

~~~jsp
<%--JSP 的注释...-->
~~~

#### 指令

~~~jsp
<%@ page ......%>			<%--配置指令--%>
~~~

~~~jsp
<%--@include会将两个页面合二为一 --%>
<%-- 最好不用，因为可能会出现变量重名的问题 --%>
<%@ include file="path"%>
// 界面 。。。

<%--JSP 标签会拼接页面,本质还是两个--%>
<%-- 一般使用这个，更灵活 --%>
<jsp:include page="path" />
// 界面 。。。
~~~

## 九大内置对象

- PageContext			存东西
- Request                     存东西
- Response
- Session                      存东西
- Application                存东西
- config                     
- out     
- page（几乎不用）
- exception

#### 作用域

~~~java
pageContext.setAttribute("name1","一号");	// 保存的数据只在一个页面中，出了当前页面就没了
request.setAttribute("name2","二号"); 	// 保存的数据只在一次请求中，请求转发会携带这个信息
session.setAttribute("name3","三号"); 	// 保存的数据会在一次会话中，关闭浏览器就没了
application.setAttribute("name4","四号"); // 保存的数据只在服务器中有效，关闭服务器就没了
~~~

request ：用户看完就没用了。							   比如：新闻

session：用户看完等下还有用。							 比如：购物车

application：一个用完，另一个用户还可能使用。比如：聊天数据

## JSP标签、JSTL标签、EL表达式

> 用之前要导包 用到的包：javax.servlet.jsp.jst1和taglibs

~~~xml
<dependency>
    <groupId>javax.servlet.jsp</groupId>
    <artifactId>javax.servlet.jsp-api</artifactId>
    <version>2.3.3</version>
</dependency>
<!-- 使用jstl标签的包 -->
<dependency>
    <groupId>taglibs</groupId>
    <artifactId>standard</artifactId>
    <version>1.1.2</version>
</dependency>
~~~

#### EL表达式			${ }

- 获取数据
- 执行运算
- 获取web开发的常用对象

#### JSP标签

~~~jsp
<%--这么写产生的结果是 http://localhost:8080/jsptagtest02.jsp?name=nihao$age=20--%>
<%--<jsp:forward page="">不加东西就跳转到"/"--%>
    <jsp:forward page="/jsptagtest02.jsp">
        <jsp:param name="name" value="nihao"/>
        <jsp:param name="age" value="20"/>
    </jsp:forward>
<%=request.getParameter("name")%>
~~~

#### JSTL 标签

使用 JSTL 标签使用为了弥补HTML标签的不足，自定义了许多标签，标签的功能和 Java代码一样

> 需要引入JSTL 核心标签库，才可以使用JSTL标签

~~~xml
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
~~~



懒得写了，麻烦·······用到就百度吧
