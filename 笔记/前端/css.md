##  CSS导入方式

优先级问题：**就近原则**

- 行内样式：在标签元素中，编写一个style属性

~~~html
<h1 style = "color:red"> </h1>
~~~

- 内部样式

~~~html
<style>
	h1{color:red;}
</style>
~~~

- 外部样式

~~~html
<link rel="stylesheet" href="path">
~~~

## 选择器

>  作用：选择页面上的某一个或者某一类元素

优先级问题：不遵守就近原则 **id选择器 >  class 选择器 > 标签选择器**

#### 基本选择器

 **选中多个元素用逗号隔开**

###### 标签选择器

会选择到页面上所有的这个标签的元素

~~~css
<style>
	h1{color:red;}
</style>
<h1> </h1>
~~~

###### 类选择器（class）

好处：可以复用

~~~css
<style>
	.name{color:red;}
</style>
<h1 class = "name"> </h1>
<h2> </h2>
~~~

###### id选择器

id选择器必须**全局唯一**，不可复用

~~~css
<style>
	#name{color:red;}
</style>
<h1 id="name"> </h1>
<h2> </h2>
~~~

#### 层次选择器

###### 后代选择器

~~~css
/*h1标签的所有后代p标签都生效*/
<style>
	h1 p{color:red;}
</style>
<h1>
	<p></p>/*这个生效*/
	<p></p>/*这个生效*/
</h1>
~~~

###### 子选择器

~~~~css
/*h1标签的一代p标签才生效，在下一级不生效*/
<style>
	h1>p{color:red;}
</style>
<h1>
    <p></p>/*这个生效*/
    <p></p>/*这个生效*/
    <ul>
        <p></p>/*这个不生效*/
    </ul>
</h1>
~~~~

###### 相邻兄弟选择器

~~~css
/*会作用在同级。只有一个，相邻的(下面)*/
<style>
	.classname + p {color:red;}
</style>
<p>p0</p>
<p class = "classname">p1</p>
<p>p2</p>	/*这个才生效*/
<p>p3</p>
~~~

###### 通用兄弟选择器

~~~css
/*会作用在同级下面所有的*/
<style>
	.classname~p {color:red;}
</style>
<p>p0</p>
<p class = "classname">p1</p>
<p>p2</p>	/*生效*/
<p>p3</p>	/*生效*/
~~~

#### 结构伪类选择器

伪类 "**`：`**" 叫作伪类。向某些选择器添加特殊的效果

~~~css
ul li:first-child{	}/*	ul的第一个子元素 */
ul li:last-child{	}/*	ul的最后一个子元素 */

p:nth-of-type(数字){	}/* 定位到p标签的父级元素，选择第"数字"个元素*/
~~~

#### 属性选择器

id加class

语法 ： **元素名字[元素类型="元素名字"]{值}**

**`[]`**中常用正则：

- 绝对等于		 	**`=`**
- 包含这个元素     **`*=`**
- 以这个开头         **`^=`**
- 以这个结尾         **`$=`**

~~~css
<style>
	a{/*一些样式*/}
	a[id]{/*一些样式*/}		/*存在id的元素*/
	a[id=123]{/*一些样式*/}/*id=123的元素*/
	a[class *= "name1"]{}/*类选择器包含name1的元素*/
</style>
<a href="..." class="name1 name2 name3" id="id">
~~~

## 文本样式

color：RGB	RGBA(有透明度，取值范围：0-1)

text-align：排版

text-indent：段落首行缩进 

line-height ： 行高，设置和高度一致，就可以上下居中

text-decoration：underline 设置下划线

text-shadow： x轴 y轴 阴影程度(越大越淡) 颜色；设置阴影

## 列表样式

list-style：none 	去点

## 背景图片应用

backgroup-image：url

backgroup-repeat:no-repeat   	设置不平铺

backgroup-position ：x轴 y轴 	**图片位置**

#### 渐变

[css渐变色](https://www.grabient.com/)
background: linear-gradient(315deg, #414cd0 0%, #C850C0 28%, #FFCC70 100%);

## 盒子模型

盒子具体多大计算方式：**外边距+内边距+边框+内容**

margin	外边距

padding 内边距

border 边框

margin：**上** **右** **下** **左**(顺时针) ；三个值是**上**，**左右**，**下**；两个值是**上下**，**左右**；一个值是四周

#### 居中：

margin：0 auto; 有些情况下没居中加上text-align:center;

也可以套div来做居中

>  使用边框画三角

~~~css
width: 0px;
height: 0px;
border: 100px solid;
border-color: red #fff #fff #fff;
~~~

#### 阴影

box-shadow：x轴 y轴 阴影程度(越大越淡) 颜色;

## 圆角边框

border-radius：**左上** **右上** **右下** **左下**（顺时针） 

**圆圈 ： 圆角=半个宽度+边框厚度**

## 浮动

#### 标准文档流

> 标准文档流遵循 从左到右，从上到下

块级元素：   独占一行

行内元素：不独占一行

display:inline				转为行元素

display:block				转为块元素

display:inline-block	转为行内块元素

```css
float：left;/*向左浮动*/
clear:both; /*清除浮动*/
```

#### 父级边框塌陷的问题

解决方案：

- 增加父级标签高度
    - 元素假设有了固定的高度，就会被限制

- 在大的div最下面加上一个空的div，并清除那个空div的浮动
    - 代码中尽量避免空div

~~~css
clear: both;margin: 0;padding: 0;
/*可能存在被忽视的可能，加上一个&nbsp;试试*/
~~~

- 设置overflow(设置在父级元素中)
    - 下拉的一些场景避免使用

~~~
overflow:hidden
~~~

- 父类添加一个伪类 `：after`和添加空div差不多。推荐这种方法

~~~css
.father:after{
    content:'';
    display:block;
    clear:both;
}
~~~

#### 滚动条

~~~css
overflow:visible	/*内容不会被修剪，但会呈现在元素框之外*/
overflow:hidden		/*内容会被修剪，其余内容是不可见的*/
overflow:scroll		/*内容会被修剪，但会出现滚动条查看其余内容*/
~~~

## 定位

position

~~~css
top:相对于上面进行偏移
top：20px	其实是相当于向下偏移了20px
top：-20px	其实是相当于向上偏移了20px
~~~

#### 相对定位

position:relative	

相对于**原来的位置**进行指定的偏移，它仍然在标准文档流中，原来的位置会被保留

~~~css
position：relative;
top:-20px;
~~~

#### 绝对定位

position：absolute

相对于**父级或者浏览器**进行指定的偏移，它不在标准文档流中，原来的位置不会被保留

- 如果父级元素没有定位的情况下，会依照浏览器定位
- 父级元素存在定位的情况下，会依照父级进行定位（可以給父级元素设置position：relative）
- 会在父级元素之内移动

#### 固定定位

position：fixed

相对于浏览器的位置定死

#### z-index

图层概念。默认是与父级相同，可以为负数，可以无限制高

## 动画

#### animate

~~~bash
npm install animate.css
# 随便找个地方下载下来，然后把其中的animate.css拷贝出来
~~~

~~~html
<!DOCTYPE html>
<html lang="en">
    <head>
        <title>Document</title>
        <!--引入-->
        <link rel="stylesheet" type="text/css" href="./animate.css"/>
    </head>
    <body>
        <h1 
            /* animate__官网说要加上 */	/*animated fadeInDown(动画名) infinite(加上就一直，不加就播放一遍)*/
            class="animate__animated animate__fadeInDown animate__infinite" 
            style="background-color: red;">An animated element</h1>
    </body>
</html>
~~~

