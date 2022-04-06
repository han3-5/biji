## 分布式

> 分布式系统是若干个独立计算机的集合，这些计算机对于用户来说就像一个相关系统
>
> 分布式系统是由一组通过网络进行通信、为了完成共同的任务而协调工作的计算机节点组成的系统。为了用廉价的、普通的机器完成单个计算机无法完成的计算、存储任务

## RPC

> Remote Procedure Call  远程过程调用
>
> 是一种技术思想，不是一种规范。允许程序调用另一个地址空间的过程或函数（A电脑上的方法调用B电脑上的方法）

两个核心：**通讯** 		**序列化**

## Dubbo

> 是一款高性能、轻量级的开源 Java RPC 框架
>
> 六大核心(其中三个)：面向接口的远程方法调用，智能容错和负载均衡、服务自动注册和发现

[Apache Dubbo](https://dubbo.apache.org/zh/)

![](./images/Dubbo01.png)

**服务提供者**（Provider）：暴露服务的服务提供方，服务提供者在启动时，向注册中心注册自己提供的服务。

**服务消费者**（Consumer）：调用远程服务的服务消费方，服务消费者在启动时，向注册中心订阅自己所需的服务，服务消费者，从提供者地址列表中，基于软负载均衡算法，选一台提供者进行调用，如果调用失败，再选另一台调用。

**注册中心**（Registry）：注册中心返回服务提供者地址列表给消费者，如果有变更，注册中心将基于长连接推送变更数据给消费者

**监控中心**（Monitor）：服务消费者和提供者，在内存中累计调用次数和调用时间，定时每分钟发送一次统计数据到监控中心

## zookeeper

> Zookeeper是 Apache Hadoop 的子项目，是一个树型的目录服务，支持变更推送，适合作为 Dubbo 服务的**注册中心**，工业强度较高

#### 下载安装

[Index of /zookeeper/zookeeper-3.8.0 (apache.org)](https://dlcdn.apache.org/zookeeper/zookeeper-3.8.0/)

下载带 bin的

下载后运行 `/bin/zkServer.cmd` ，这个时候可能会闪退，解决方案：编辑 `zkServer.cmd` 文件尾加上pause

然后会有一个报错信息` Reading configuration from: D:\Environment\apache-zookeeper-3.8.0-bin\bin\..\conf\zoo.cfg` 是说没找到这个`zoo.cfg` 文件

进入`/conf` 目录下，复制一份 `zoo_sample.cfg`  将名字改为 `zoo.cfg`

~~~bash
# zoo.cfg 文件其中两个配置
dataDir=/tmp/zookeeper				# 临时文件存放的位置
clientPort=2181						# 端口
~~~

## 安装 Dubbo-admin

> dubbo本身并不是一个服务软件。它其实就是一个jar包，能够帮你的java程序连接到zookeeper，并利用zookeeper消费、提供服务。
>
> 是一个监控管理后台

1. 下载   （当前下载的是0.2.0）

地址 ：https://github.com/apache/dubbo-admin/tree/master

2. 在项目目录下打包dubbo-admin 

~~~bash
mvn clean package -Dmaven.test.skip=true
~~~

打的包在`\dubbo-admin-master-0.2.0\dubbo-admin\target` 目录下

3. 启动 zookeeper
4. 启动刚打的jar包
5. 测试 http://localhost:7001

账户密码是root-root

