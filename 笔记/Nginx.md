**Nginx**  是一个高性能的 **HTTP** 和 **反向代理** web服务器，同时也提供了IMAP/POP3/SMTP服务。

## 下载

[nginx下载](http://nginx.org/en/download.html)

使用稳定版本（Stable version）

## 安装

#### windows

下载好之后直接解压运行就启动了

最好使用cmd运行而不要直接双击，双击会一闪而过，不知道什么情况

#### linux

下载之后解压，执行**`configure`**进行自动配置，之后执行 **`make`** 来处理依赖，如果不放心，可以使用 **`make install`**

使用 **`whereis nginx`** 找到nginx在哪里，进入目录，进入**`sbin`** 来启动nginx

## 常用命令

~~~bash
cd /usr/local/nginx/sbin
./nginx				# 启动
./nginx -s stop 	# 强行停止
./nginx -s quit		# 安全停止
./nginx -s reload	# 重新加载配置文件
ps aux | grep nginx # 查看nginx进程
~~~

## 配置

**全局块**

>  从配置文件开始到events之前

~~~bash
worker_processes 1;	# 值越大，可以支持的并发处理量就越多
~~~

**events**

>  涉及的指令主要影响Nginx服务器与用户的网络连接

~~~bash
worker_connections  1024; # 支持的最大连接数
~~~

**http**

一个server 节点就是一个虚拟主机

~~~bash
server {
    listen       80;			# 监听端口	
    #server_name  localhost;	
    
# 反向代理    
    location ~ /t1 {
    proxy_pass http://localhost:8080;	# #代理到了xxx

}
    
    location / {		
    proxy_pass http://localhost:8080; #代理到了xxx
}
~~~

#### location 详解

语法规则： **`location [=|~|~*|^~] /url/ { … }`**

> location /t1/

- `~` 开头表示区分大小写的正则匹配
- `~*` 开头表示不区分大小写的正则匹配  
- `/` 通用匹配，任何请求都会匹配到。

**root 和 alias**

~~~bash
# 使用alias时，目录名后面一定要加"/"
location /img/ {
	alias /var/www/image/;
}
#若按照上述配置的话，则访问/img/目录里面的文件时，ningx会自动去/var/www/image/目录找文件
location /img/ {
	root /var/www/image;
}
#若按照这种配置的话，则访问/img/目录下的文件时，nginx会去/var/www/image/img/目录下找文件
~~~

#### 负载均衡

在 http 块中加入**upstream xxx{	}**

~~~bash
http{
	upstream 名字A{
	  # ip_hash;	# 每个请求按ip的hash结果分配，每个访客固定访问一个后端服务器，可以解决session的问题
	  # least_conn; # 将请求分配到连接数最少的服务器上
		server ip地址:端口 weight=5;# weight权重
		server ip地址:端口 weigth=1;# 只有上面的五分之一
		# 轮询（默认）
		# fair; #根据响应访问时间，先去响应快的
	}
	server{
    listen       80;			# 监听端口	
    #server_name  localhost;		
    location / {
    	proxy_pass http://名字A;
    }
	}
}
~~~

#### 动静分离

~~~bash
location ~ /*.(txt|gif) {		# 所有(txt|gif)后缀的文件都被分离到了xxx目录下
    root C:/Users/zyh/Desktop/;	# 保存着静态资源
}
# 这样就可以让tomcat只进行动态资源的交互，静态资源被分开
location / {
    proxy_pass http://localhost:8080; 
}
~~~

