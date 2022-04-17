> 来自B站up主  IT老哥[IT老哥的个人空间_哔哩哔哩_bilibili](https://space.bilibili.com/526653251)

## Codota

只需要点一下，便可以获取到在github、stackoverflow等上排名最高的片段，并在IDE中显示出来，更快、更方便。

如果你要找某个类的示例代码，只需要选择某个类名，点击右键选择Get relevant examples 或快捷键 ctrl+shift+O，如下图

![图片](https://img-blog.csdnimg.cn/img_convert/17f52962f3f75cd1426537b9b0f945f3.png)

![图片](https://img-blog.csdnimg.cn/img_convert/fdff8e42c3db03187164e86199ac2ebb.png)

## *MyBatis Log Plugin

> 有时候我们需要运行过程中产生的SQL语句来帮助我们排查某些问题，这款插件可以把Mybatis输出的SQL日志还原成完整的SQL语句，就不需要我们去手动转换了。

首先我们需要打开这款插件的窗口；

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/5/12/17208eae3956eed2~tplv-t2oaga2asx-watermark.awebp)

当我们调用方法，控制台输出Mybatis的SQL日志时；

```sql
2020-04-28 15:52:20.455 DEBUG 13960 --- [nio-8081-exec-1] c.m.m.m.UmsAdminMapper.selectByExample   : ==>  Preparing: select id, username, password, icon, email, nick_name, note, create_time, login_time, status from ums_admin WHERE ( username = ? ) 
2020-04-28 15:52:20.456 DEBUG 13960 --- [nio-8081-exec-1] c.m.m.m.UmsAdminMapper.selectByExample   : ==> Parameters: admin(String)
2020-04-28 15:52:20.463 DEBUG 13960 --- [nio-8081-exec-1] c.m.m.m.UmsAdminMapper.selectByExample   : <==      Total: 1

```

该插件会自动帮我们转换成对应的SQL语句；

```sql
1  2020-04-28 15:50:40.487 DEBUG 9512 --- [nio-8081-exec-9] c.m.m.m.UmsAdminMapper.selectByExample   : ==>
select id, username, password, icon, email, nick_name, note, create_time, login_time, status
 FROM ums_admin
 WHERE ( username = 'admin' );

```

有的时候我们需要转换的日志并不在自己的控制台上，这时可以使用插件的`SQL Text`功能：

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/5/12/17208eae3a697f21~tplv-t2oaga2asx-watermark.awebp)

直接复制我们需要转换的日志，然后点击`Restore Sql`按钮即可。

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/5/12/17208eae646446ef~tplv-t2oaga2asx-watermark.awebp)



## *RestfulToolkit

> 一套Restful服务开发辅助工具集，提供了项目中的接口概览信息，可以根据URL跳转到对应的接口方法中去，内置了HTTP请求工具，对请求方法做了一些增强功能，总之功能很强大！

可以通过右上角的`RestServices`按钮显示项目中接口的概览信息；

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/5/12/17208eae625c7169~tplv-t2oaga2asx-watermark.awebp)

可以通过搜索按钮，根据URL搜索对应接口；

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/5/12/17208eae6a4e4d88~tplv-t2oaga2asx-watermark.awebp)

可以通过底部的HTTP请求工具来发起接口测试请求；

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/5/12/17208eae6b2244d2~tplv-t2oaga2asx-watermark.awebp)

通过在接口方法上右键可以生成查询参数、请求参数、请求URL；

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/5/12/17208eae6f43b2f8~tplv-t2oaga2asx-watermark.awebp)

通过在实体类上右键可以直接生成实体类对应的JSON；

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/5/12/17208eae754c9d7e~tplv-t2oaga2asx-watermark.awebp)



## GsonFormat

> 这款插件可以把JSON格式的字符串转化为实体类，当我们要根据JSON字符串来创建实体类的时候用起来很方便。

- 首先我们需要先创建一个实体类，然后在类名上右键`Generate`，之后选择`GsonFormat`；

  ![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/5/12/17208eae997a872f~tplv-t2oaga2asx-watermark.awebp)

- 输入我们需要转换的JSON字符串：

  ![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/5/12/17208eaea03e4151~tplv-t2oaga2asx-watermark.awebp)

- 选择性更改属性名称和类型：

  ![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/5/12/17208eaeaf6a3301~tplv-t2oaga2asx-watermark.awebp)

- 点击确定后直接生成实体类。

  ![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/5/12/17208eaebe23f2d6~tplv-t2oaga2asx-watermark.awebp)



## Grep Console

> 一款帮你分析控制台日志的插件，可以对不同级别的日志进行不同颜色的高亮显示，还可以用来按关键字搜索日志内容。

- 当项目打印日志的时候，可以发现不同日志级别的日志会以不同颜色来显示；

  ![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/5/12/17208eaeb1115ddd~tplv-t2oaga2asx-watermark.awebp)

  

- 如果你需要修改配色方案的话，可以通过`Tools`打开该插件的配置菜单；

  ![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/5/12/17208eaec20847bd~tplv-t2oaga2asx-watermark.awebp)

- 然后通过配置菜单修改配色方案；

  ![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/5/12/17208eaec42af774~tplv-t2oaga2asx-watermark.awebp)

- 可以通过在控制台右键并使用`Grep`按钮来调出日志分析的窗口：

  ![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/5/12/17208eaec6bd4c36~tplv-t2oaga2asx-watermark.awebp)

- 然后直接通过关键字来搜索即可。

  ![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/5/12/17208eaed56b6d35~tplv-t2oaga2asx-watermark.awebp)



## Maven Helper

> 解决Maven依赖冲突的好帮手，可以快速查找项目中的依赖冲突，并予以解决！

- 我们可以通过`pom.xml`文件底部的`依赖分析`标签页查看当前项目中的所有依赖；

  ![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/5/12/17208eaf004f2cfd~tplv-t2oaga2asx-watermark.awebp)

- 通过`冲突`按钮我们可以筛选出所有冲突的依赖，当前项目`guava`依赖有冲突，目前使用的是`18.0`版本；

  ![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/5/12/17208eaf0054df0f~tplv-t2oaga2asx-watermark.awebp)

- 选中有冲突的依赖，点击`Exclude`按钮可以直接排除该依赖；

  ![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/5/12/17208eaf053688d7~tplv-t2oaga2asx-watermark.awebp)

- 同时`pom.xml`中也会对该依赖添加`<exclusion>`标签，是不是很方便啊！

  ![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/5/12/17208eaf09f59014~tplv-t2oaga2asx-watermark.awebp)



## Statistic

- 我们可以通过顶部菜单中的`View->Tool Windows->Statistic`按钮开启该功能；

  ![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/5/12/17208eaf1b8d29c5~tplv-t2oaga2asx-watermark.awebp)

- 此时就可以看到我们项目代码的统计情况了，比如我的开源项目`mall`中`java`代码大小为`2818kB`，行数为`85645`。

  ![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/5/12/17208eaf220bf1d7~tplv-t2oaga2asx-watermark.awebp)



## Key Promoter X 快捷键插件

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/dddceef275ad46fc978727991076cb90~tplv-k3u1fbpfcp-watermark.awebp)

无需鼠标的编码速度更快、效率更高，这已经不是什么秘密了，但是当IntelliJ IDEA有这么多快捷键需要记住时，你怎么能以键盘为中心呢？

它会训练你使用它们，就像一个持久而细致的coach一样，当您单击IDE中的元素时，它将显示一个带有相关快捷方式的工具提示。此外，对于没有快捷方式的按钮，Key promotor X会提示您创建快捷方式。

熟能生巧！过了一段时间，你会发现你下意识地保存自己的点击和使用必要的快捷方式。



## Rainbow brackets 花括号插件

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2d9af77cd27f47f78e4290e6afefdb23~tplv-k3u1fbpfcp-watermark.awebp)



## GenerateAllSetter

可以直接生成这个对象的所有set方法，非常方便。

将光标放在变量声明的那一行，注意不能是分号后面。然后按快捷键Alt + Enter，就会弹出菜单供你选择。

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/16/17181ec2e9dd3ea2~tplv-t2oaga2asx-watermark.awebp)

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/16/17181ec2ebb0cda3~tplv-t2oaga2asx-watermark.awebp)





## HighlightBracketPair —— 括号开始结尾 高亮显示。

![图片](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6eba84edf59645bc9858f3020d40cf3c~tplv-k3u1fbpfcp-watermark.awebp)

![图片](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/159118c6774e44eb96eeddcc38d54263~tplv-k3u1fbpfcp-watermark.awebp)

![图片](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/47a21413b6f240cfaa77e51b4a5e95b2~tplv-k3u1fbpfcp-watermark.awebp)



## google-java-format —— 代码自动格式化

这个插件的优点在于不需要手动快捷键去格式化代码

## JUnit Generator V2.0

自动生成测试代码。

![IntelliJ IDEA 2020.2.4款 神级超级牛逼插件推荐（自用，真的超级牛逼）_IDEA_16](https://s5.51cto.com/images/blog/202105/28/f3cc903eec688a6bcc847880c6261b4d.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

![IntelliJ IDEA 2020.2.4款 神级超级牛逼插件推荐（自用，真的超级牛逼）_IDEA教程_17](https://s7.51cto.com/images/blog/202105/28/b74315a2c3d7755df958d6f1d3dd0188.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

