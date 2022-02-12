## 导入javaScript

- script标签内，写javascript代码

~~~
<script> 代码 </script>
~~~

- 外部引入

~~~
<script src="path"> </script>
~~~

## 基本语法

参照 **Java**

#### 比较运算符

~~~javascript
=	//赋值
==	//等于（类型不一样，值一样，会判断为true）
=== //绝对等于(必须类型和值都一样才为true)		建议使用
~~~

## 浏览器控制台使用

~~~java
console.log() //在浏览器的控制台打印
~~~

elements 元素 

console   控制台

sources源代码，里面可以打断点调试

network 网络，抓包用

application 应用程序，里面有cookie之类

## 数据类型

#### 变量

var 声明	数字字母下划线$，不能以数字开头，甚至可以使用中文

ES6之后的局部变量建议都使用 **`let `**定义，全局变量还是使用 **`var`**

~~~javascript
var i = 1;		//ES6之前定义
let i = 1;		//ES6之后的局部变量，全局变量还是使用 var
~~~

#### number

js不区分小数和整数

尽量避免使用浮点数运算，存在精度问题

~~~javascript
1/3 === (1-2/3); //false
~~~

~~~javascript
NaN	//NaN表示Not a Number，当无法计算结果时用NaN表示
NaN===NaN //false//NaN 和任何数都不相等，包括和自己
~~~

#### 字符串

'abc'	"abc"	js也是字符串不可变

- 可以多行字符串编写

~~~javascript
var str = `hello
你好
123`
~~~

- 模板字符串	${**内容**(可以是变量名)}

~~~javascript
var str1 = `nihao${str}`
~~~

#### 布尔值

true 、false

#### 逻辑运算

~~~
$$		两个都为真，结果为真
||		一个为真，结果为真
!		真变假，假变真
~~~

#### 数组

Java 的数组必须是相同类型的对象，js中不需要这样

Java中的数组不可变长，**js中数组可以变长**。

~~~javascript
var arr = [1,2,3,4,5]
var arr = new Array(1)
arr[0]			//取值
arr.length = 10	//給长度赋值
~~~

**slice()** 截取Array的一部分，返回一个新数组，类似于String中的substring()

**push() pop()** 用于js的数组給数组加元素减元素用，在队尾加减

**unshift() shift()** 														用于对头加减

**sort()** 排序

**reserve()** 元素反转

**concat()**  将数组添加到当前数组。但不会修改数组，只会返回一个新的数组

**连接符 join**

~~~javascript
var arr = ["A","B","C"]
arr.join('-')	//会返回A-B-C
~~~

###### 遍历

~~~javascript
var arr = [1,2,3]
for(let x in arr){
    //in 遍历的下标
}
for(let x of arr){
    //of 遍历的value
}
~~~

#### 对象

**JavaScript中的对象是一组由键-值组成的无序集合**

**JavaScript中的所有的键都是字符串，值是任意对象**

对象是大括号，数组是中括号

>  每个属性使用`","`隔开，最后一个不需要添加

~~~javascript
var 对象名 = {
    name:"nihao",
    age:123,
    a1:[1,2,"a",···]
}
//取值
name.n1
~~~

> Js中对象，{......}表示一个对象，键值对描述属性 xxx：xxx，多个属性之间使用逗号隔开

- 对象赋值

~~~javascript
对象名.name = "yiersan"
~~~

- 使用一个不存在的对象属性，不会报错。会出现**undefined**

~~~javascript
对象名.haha
undefined
~~~

- 动态添加和删除

~~~javascript
delete 对象名.name		//删除name属性
对象名.haha			//添加haha属性
~~~

- 判断属性值是否在其中 	xxx in xxx

~~~javascript
'name' in 对象名
'toString' in 对象名			  //返回值为true	//JavaScript也有继承
对象名.hasOwnProperty('属性名')	//判断该属性名是不是自己单有的，不是继承来的
~~~

#### Map

ES6新特性

~~~javascript
var m1 = new Map(['tom',100],['jack',20]);
m1.get('tom');			//通过key获取value
m1.set('admin',123456); //新增或者修改
m1.delete('tom');		//删除
~~~

#### Set

ES6新特性。无需不重复的集合

~~~javascript
set.add();		//添加
set.detlete();	//删除
set.has();		//判断该元素是否在其中
~~~

## 严格检查模式strict

'use strict';	严格检查模式，预防JavaScript 的随意性导致产生的一些问题

如果使用 'use strict' 报错，是因为没设置支持ES6.最好放在JavaScript第一行