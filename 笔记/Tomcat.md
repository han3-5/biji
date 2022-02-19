## Tomcat

#### 安装

[tocmat官网](https://tomcat.apache.org/)

下载之后解压就ok了

#### linux下

1. 解压这个文件

~~~bash
tar -zxvf  apache-tomcat-9.0.58.tar.gz
~~~

2. 启动tomcat

~~~bash
./startup.sh		# 启动
./shutdown.sh		# 停止
~~~

#### linux下启动慢的情况

- **`find / -name java`**找到java 的jre目录
- 打开**`jre/lib/security/java.security`**文件
- 大概在一百多行的位置，更换如下

~~~bash
securerandom.source=file:/dev/urandom
# 上面的更改为下面
securerandom.source=file:/dev/./urandom
~~~

- 重启tomcat

#### 配置

主要修改的配置文件是**`conf`**下的**`server.xml`**目录

**配置端口号：**

~~~xml
<Connector port="8080" protocol="HTTP/1.1"
           connectionTimeout="20000"
           redirectPort="8443" />
~~~

**配置主机的名称：**

~~~xml
<Host name="localhost"  appBase="webapps"
      unpackWARs="true" autoDeploy="true">
~~~

#### 改变默认访问路径

> 可以先将root中的目录清掉，将文件放进来

> 也可以更改server.xml 文件内容

~~~html
<!--<Host></Host> 标签中加入<Context> 标签-->
<Context docBase="/test" path="" reloadble="true" />
<!-- docBase 放入项目目录-->
~~~

## 文件结构

~~~
--webapps：Tomcat服务器的web目录
	--ROOT
	--xxxxxx : 网站的目录名
		--WEB-INF
			--classes：java程序
			--lib	：web应用所依赖的jar包
			--web.xml:网站配置的文件
		--index.html：默认的首页
		--static
			--css
				--style.css
			--js
			--img
		.
~~~

