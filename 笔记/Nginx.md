**Nginx**  是一个高性能的 **HTTP** 和 **反向代理** web服务器，同时也提供了IMAP/POP3/SMTP服务。

## 下载

[nginx下载](http://nginx.org/en/download.html)

使用稳定版本（Stable version）

## 安装

#### windows

下载好之后直接解压运行就启动了

最好使用cmd运行而不要直接双击，双击会一闪而过，不知道什么情况

#### linux

1. 安装依赖包

~~~bash
yum -y install gcc pcre-devel zlib-devel openssl openssl-devel
~~~

2. 下载之后解压，执行**`configure`**进行自动配置，之后执行 **`make`** 来处理依赖，如果不放心，可以使用 **`make install`**

~~~bash
tar -zxvf nginx-1.20.2.tar.gz 
cd nginx-1.20.2
./configure 											 # 进入解压目录后执行配置命令 
./configure --prefix=/usr/local/nginx	  # 指定安装路径
make && make install						    # 编译并安装
~~~

如果出现 ./configure: error: the HTTP gzip module requires the zlib library.
You can either disable the module by using --without-http_gzip_module
option, or install the zlib library into the system, or build the zlib library
statically from the source with nginx by using --with-zlib=<path> option.

则执行 `yum install -y zlib-devel`

3. 使用 **`whereis nginx`** 找到nginx在哪里，进入目录，进入**`sbin`** 来启动nginx

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
worker_processes 1;	# 值越大，可以支持的并发处理量就越多(worker数)
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
    #server_name  localhost;	域名
    
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
#若按照上述配置的话，则访问/img/目录里面的文件时，ningx会自动去/var/www/image/目录下找文件
# 如 xxx/var/www/image/img/test.jpg 查找为 xxx/var/www/image/test.jpg
location /img/ {
	root /var/www/image;
}
#若按照这种配置的话，则访问/img/目录下的文件时，nginx会去/var/www/image/img/目录下找文件
# 如 xxx/img/test.jpg 查找为 xxx/var/www/image/img/test.jpg
~~~

#### 负载均衡

在 http 块中加入**upstream xxx{	}**

~~~bash
http{
	upstream 名字A{
	  	 # ip_hash;	# 每个请求按ip的hash结果分配。这样做可以使每个访客固定访问一个后端服务器，可以解决session的问题
	 	 # least_conn; # 将请求分配到连接数最少的服务器上
		 # fair; #根据响应访问时间，先去响应快的
         # 轮询（默认）
         server ip地址:端口 weight=5;# weight权重
         server ip地址:端口 weigth=1;# 只有上面的五分之一
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

#### 高可用

> 高可用其实就是预防 nginx 宕机了，所以增加一台从 nginx

1. 两台服务器都装上了 **nginx**

2. 两台服务器都装上了 **keepalived**

3. 配置主从

    1. 修改**`/etc/keepalived/keepalivec.conf`** 配置文件

    ~~~bash
    global_defs{
    	notification_email{
    		acassen@firewall.loc
    		failover@firewall.loc
    		sysadmin@firewall.loc
    	}
    	notification_eamil_from Alexandre.Cassen@firewall.loc
    	smtp_server xxxxxxxxxx # ip 地址
    	smtp_connect_timeout 30
    }
    vrrp_script chk_http_port{
    	script "/usr/local/src/nginx_check.sh" # 检测脚本位置
    	interval 2 					# 检测脚本执行的间隔 2 s
    	weight						# 权重
    }
    vrrp_instance VI_1{
    	state MASTER			# 主服务器是MASTER
    	# state BACKUP			# 备份服务器时 BACKUP
    	interface ens0			# ifconfig后显示的网卡名
    	virtual_router_id 51	# 主、备机的值必须相同
    	priority 100			# 主、备机优先级，主机较大
    	advert_int 1			# 发送信号检测是否活着 1 s
    	authentication{			# 校验方式 
    		auth_type PASS		
    		auth_pass 1111
    	}
    	virtual_ipaddress{
    		xxx.xxx.xxx.xxx		# 虚拟地址，通过这个访问到nginx
    	}
    }
    ~~~
    
    2. 在**`/usr/local/src`** 目录下添加一个检测脚本
    
    > 在 /usr/local/src 目录下是因为 keepalived.conf 配置文件中写的
    
    ~~~bash
    #！/bin/bash
    A=`ps -C nginx -no-header | wc -1`
    #A=`ps -C nginx --no-header | wc -1`可能是这个
    if	[$A -eq 0];then
    	/usr/local/nginx/sbin/nginx 	# nginx启动的地址
    	sleep 2							# 检测到nginx挂了
    	if[ `ps -C nginx --no-header | wc -1` -eq 0];then
    		killall keepalived			# 让备份服务器做
    	fi
    fi
    ~~~

## 原理

- 一个nginx中包含 master 和 worker
- client发送一个请求，由master接受管理，worker 争抢这个请求
- 好处：
    - 可以进行热部署
    - 每个worker都是独立的进程，不需要加锁
- 设置多少个 worker 最好
    - Nginx和redis类似都采用了 io多路复用机制
    - 一般来说设置和cpu核心数一致
- 最大支持的并发数
    - 普通静态访问worker_processes*worker_connections /2
    - 反向代理访问worker_processes*worker_connections /4
