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

