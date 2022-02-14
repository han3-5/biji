## 数据库分类

#### 关系型数据库

- Mysql、Oracle、Sql server······
- 通过表和表之间，行和列之间的关系进行数据的存储

#### 非关系型数据库

- Redis、MongoDB
- 非关系型数据库，对象存储，通过对象的自身属性来决定

#### DBMS

> DataBase Management System

数据库的管理软件，科学有效的管理我们的数据，维护和获取数据

## 数据库引擎和编码

#### MYISAM和InnoDB区别

InnoDB 是默认使用的

myisam 是早些年使用

|              | InnoDB               | myisam |
| :----------- | -------------------- | ------ |
| 事务支持     | 支持                 | 不支持 |
| 数据行锁定   | 支持                 | 不支持 |
| 外键约束     | 支持                 | 不支持 |
| 全文索引     | 不支持               | 支持   |
| 表空间的大小 | 较大，约为myisam 2倍 | 较小   |

**常规使用操作**

- myisam 	节约空间，速度较快
- InnoDB      安全性高，事务的处理，多表多用户操作

#### 字符集编码

创表语句后面加上  charset=utf8. 推荐此方法，因为代码拿了就能运行

不设置的话，mysql默认字符集编码是**Latin1**，不支持中文

> 也可以在my.ini文件中配置默认的编码

~~~ini
character-set-server=utf8
~~~

## 安装mysql

> sc delete mysql	清空服务;     在安装出现意外时使用

- 官网下载 [MySQL](https://www.mysql.com/)
- 最好下载压缩包，自己配置。     '.exe'不好卸载
- 配置环境变量
- 在mysql目录下新建mysql配置文件my.ini文件

~~~mysql
[mysqld]
# 路径后面加上"\"
basedir=路径\
datadir=路径\data\		   # 不要自己新建data，自动生成
port=3306					# 端口
ship-grant-tables			# 用来跳过密码验证
~~~

- 启动**管理员模式**下的CMD，并将路径切换到mysql下的**bin目录**，然后输入**`mysqld -install`** (安装mysql)
- 输入**`mysqld --initialize-insecure --user=mysql`** 初始化数据文件
- 输入 **`net start mysql`** 启动mysql
- 输入**`mysql -u root -p`** 进入到mysql管理界面 -p后面不加东西空格也不要，直接回车
- 进入后修改root密码，mysql语句后面一定要记得**`;`**

~~~mysql
update mysql.user set authentication_string=password('123456') where user='root' and Host='localhost';
~~~

- 输入**` flush privileges;`**刷新权限
- 修改my.ini 文件的跳过密码验证(可以注释掉)

## 数据库的列和字段

#### 数据库的列类型

> 数值

- tinyint			十分小的数据	1个字节
- smallint          较小的数据        2个字节
- int                   标准的整数        4个字节
- bigint             较大的数据         8个字节
- float               单精度浮点数      4个字节
- double           双精度浮点数     8个字节
- decimal         字符串形式的浮点数   金融计算的时候，一般使用

> 字符串

- char				  字符串固定的大小 		0~255
- **varchar           可变字符串                    0~65535**     常用
- tinytext             微型文本                       
- text                    文本串                            保存大文本

> 时间日期

- date					YYYY-MM-DD 			日期时间
- time                     HH:mm:ss                 时间格式
- **datetime             YYYY-MM-DD HH:mm:ss**   常用
- timestamp           时间戳              1970.1.1到现在的毫秒数  常用
- year                       年份表示

> null

#### 数据库的字段属性

> Unsigned

- 无符号的整数
- 声明了该列不能声明为负数

> Zerofill

- 0填充的	不足的为数，使用0来填充

> 自增

- 必须是整数类型
- 通常理解为自增，自动在上一条记录的基础上 +1(默认)
- 通常用来设计唯一的主键
- 可以自定义设计主键自增的起始值和步长

## 基础命令

~~~sql
use  数据库名;		  		 -- 切换数据库
show databases;			    -- 查看所有数据库
show tables;				-- 查看数据库中所有的表
describe 数据库名;			 -- 显示数据库中所有的表信息
exit;						-- 退出连接
~~~

> 如果不希望給大写，可以在前后加上**`** 符号。用来区别关键字和标识符

## 操作数据库

#### 操作数据库

~~~sql
create database 数据库名;	-- 创建数据库
drop database 数据库名;		-- 删除数据库
use 数据库名;				-- 使用数据库
show dababases;			   -- 显示所有数据库
~~~

~~~sql
-- [if not exists]可选项，为如果不存在 在创建
create database [if not exists] 数据库名;
~~~

#### 创建数据表

~~~sql
-- 表名 和 字段 尽量使用 `` 括起来
-- auto——increment 自增
-- comment ''	备注
-- default 设置默认值
create table if not exists `student`(
	`id` int(4) not null auto_increment comment '学号',
    `name` varchar(30) not null default '匿名' comment '姓名',
    `pwd` varchar(20) not null default '123456' comment '密码',
    `sex` varchar(2) not null default '男' comment '性别',
    `birthday` datetime default null comment '出生日期',
    `address` varchar(30) default null comment '家庭住址',
    `email` varchar(20) default null comment '邮箱',
    primary key(`id`)
 )engine=InnoDB default charset=utf8
 -- engine=InnoDB	引擎
 -- charset=utf8	设置字符集
~~~

**公式**

~~~sql
create table [if not exists] `表名`(
	'字段名' 列类型 [属性] [索引] [注释],
    ......
    '字段名' 列类型 [属性] [索引] [注释]
)[表类型][字符集设置][注释]
~~~

**可以查看语句**

~~~sql
show create database 数据库名;	 -- 查看创建数据库的语句
show create table 表名;		  -- 查看创建数据表的语句
desc 表名;					  -- 查看表的具体结构
~~~

#### 修改和删除数据表

~~~sql
-- 修改表名
alter table 旧表名 rename as 新表名;
-- 增加表的字段
alter table 表名 add 列名 类型();
-- 修改表的字段
alter table 表名 modify 列名 类型();		  	-- 不能改名字
alter table 表名 change 旧列名 新列名 类型();	  -- 可以改名字
-- 删除表的字段
alter table 表名 drop 列名;
-- 删除表(最好判断一下存在在删除)
drop table [if exists] 表名;
~~~

## 数据管理

#### 外键

保持数据一致性，完整性，主要目的是控制存储在外键表中的数据。使两张表形成关联，外键只能引用外表中列的值！

删除有外键关系的表的时候，必须先删除引用别人的表，再删除被引用的表

> 方式一  在创建表的时候，增加约束

~~~sql
key `FK_约束名` (`本表列名`),
constraint `FK_约束名` foreign key (`作为外键列名`) references `引用的表名`(`列名`);
~~~

> 方式二  在创建表的时候没有外键关系

~~~sql
alter table `表名` add constraint `约束名` foreign key (`作为外键的列名`) references `被引用的表名`(`被引用的列名`);
~~~

以上操作都是物理外键，数据库级别的外键，不建议使用 (避免数据库过多造成困扰)

**最好办法**

- 数据库就是单纯的表，只用来存数据，只有行（数据）和列（字段）
- 想使用多张表的数据，想使用外键（使用程序去实现）