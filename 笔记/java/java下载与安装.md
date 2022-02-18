## 下载

[Java Downloads | Oracle](https://www.oracle.com/java/technologies/downloads/#java8)

下载的时候可能会要求登录

1985479344@qq.com

Oracle123

## 安装





## 环境变量

#### windows

1. 新建 **`JAVA_HOME`**变量，值放入jdk包的目录
2. 新建**`CLASSPATH`**变量，值如下

~~~bash
%JAVA_HOME%/lib;%JAVA_HOME%/jre/lib
~~~

3. 配置**`Path`**，新建一个变量，值如下

~~~bash
%JAVA_HOME%/bin
~~~

~~~bash
%JAVA_HOME%/jre/bin
~~~

#### linux（有问题，别用，后面学到再改）

- 配置环境变量	在**`/etc/profile`**文件中配置

~~~bash
# 先查看jdk的路径 在 /usr/java/jdkxxxx
# 在 /etc/profile 文件里添加：
JAVA_HOME=/usr/java/jdk1.8.0_321-amd64
CLASSPATH=%JAVA_HOME%/lib;%JAVA_HOME%/jre/lib
PATH=$JAVA_HOME/bin;$JAVA_HOME/jre/bin
export PATH CLASSPATH JAVA_HOME
~~~

- 如果配置文件没有生效

~~~bash
source /etc/profile
~~~