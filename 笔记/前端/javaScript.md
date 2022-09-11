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

在JavaScript中一切皆为对象、任何 js 支持的类型都可以用JSON来表示；格式：

- 对象都用{ }
- 数组都用[ ]
- 所有的键值对 都用 key：value

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

> typeof xxx	可以用来判断 xxx 的类型

#### 变量

var 声明	数字字母下划线$，不能以数字开头，甚至可以使用中文

ES6之后的局部变量建议都使用 **`let `**定义，全局变量还是使用 **`var`**

~~~javascript
var i = 1;		//ES6之前定义
let i = 1;		//ES6之后的局部变量，全局变量还是使用 var
~~~

对象的属性名赋值方式

~~~js
p.name = '123';		// 有些情况不能使用
p['name'] = '123';		// 通用方式
// 什么时候必须使用['属性名']的方式
1. 变量名包含特殊字符，比如 ''-'' 空格
p.context-type = 'text/json'	    // 报错
p['context-type'] = 'text/json' 	// 可以使用
console.log(p['context-type']);
2. 变量名不确定
var propName = 'myAge';
var value = 18;
p.propName = value;					// 报错
p[propName] = value;				// 可以使用
console.log(p[propName]);
~~~

###### let、const、var

var：作用域为  **该语句所在的函数内，且存在变量提升现象(即先用后定义)**

let：作用域为  **该语句所在的代码块内，不存在变量提升现象**

const：不能再修改该常量的值，但如果是复杂数据类型(数组、对象)，只能浅检测

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

**repeat()** 表示将原字符串重复n次，返回一个新字符串

~~~js
'x'.repeat(3);	// xxx
~~~

- 可以多行字符串编写

~~~javascript
var str = `hello
你好
123`
~~~

- 模板字符串	${**内容**(可以是变量名)}。前提使用 **``**

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

**splice(index,howmany,·····)** 从index开始删除howmany个元素，后面是要添加到数组中的新元素

**push() pop()** 用于js的数组給数组加元素减元素用，在队尾加减

**unshift() shift()** 														用于对头加减

**sort()** 排序

**reserve()** 元素反转

**concat()**  将数组添加到当前数组。但不会修改数组，只会返回一个新的数组

**includes(value)**  数组中是否包含给定的值

**连接符 join**

~~~javascript
var arr = ["A","B","C"]
arr.join('-')	//会返回A-B-C
~~~

###### 遍历

> for...in 把一个对象的所有属性依次循环出来
>
> for...of ES6引入的新语法

~~~javascript
var arr = ['A','B','C']
for(let x in arr){
    // in 遍历的下标 得到的是String
    // 结果：0  1  2
}
for(let x of arr){
    //of 遍历的value 
    // 结果 A  B  C
}
~~~

###### filter()

> 过滤数组,filter()会返回一个新数组

~~~js
var arr = [12,21,33,17,10];
var newArr = arr.filter(function(value,index){
    return value >= 20;
})
~~~

###### some()

> 检测数组中的元素是否满足指定条件,some()返回值是**布尔值**

~~~js
// 如果找到第一个满足条件的元素，则终止循环，不再
var arr = [12,21,33,17,10];
var flag = arr.some(function(value){
    return value >= 20;
})
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

###### defineProperty()方法

> 定义对象中新属性或修改原有的属性

~~~js
// Object.defineProperty(obj,prop,descriptor)
obj：目标对象
propo：定义或修改的属性的名字
descriptor：目标属性所拥有的的特性，以对象形式 {} 书写，参数说明：
		value：设置属性的值，默认为undefined，没有则添加，存在则修改
        writable：值是否可以重写 默认为 false
		enumerable：目标属性是否可以被枚举，默认为false
        configurable：目标属性是否可以被删除或是否可以再次修改属性的特性，比如writable、enumerable，默认为false
~~~

~~~js
// 举例
var obj = {
    id:1,
    name:"test"
}
Object.defineProperty(obj,'age',{
    value:10
})
~~~



#### Map

>  ES6新特性

~~~javascript
var m1 = new Map([['tom',100],['jack',20]]);
m1.get('tom');			//通过key获取value
m1.set('admin',123456); //新增或者修改
m1.delete('tom');		//删除
m1.has();		//判断该元素是否在其中
~~~

#### Set

>  ES6新特性。无需不重复的集合

~~~javascript
var m1 = new Set(['tom','jack']);
set.add();		//添加
set.detlete();	//删除
set.has();		//判断该元素是否在其中
~~~

#### iterable

遍历更好的方式是使用iterable内置的**`forEach()`**方法

> forEach() 方式是ES5.1标准引入的

~~~javascript
a.forEach(function(element,index,array){
    // element: 指向当前元素的值
    // index: 指向当前索引
    // array: 指向Array对象本身
});
var a = ['A','B','C']
a.forEach(function(value){
    console.log(value);
})
~~~

## 严格检查模式strict

'use strict';	严格检查模式，预防JavaScript 的随意性导致产生的一些问题

如果使用 'use strict' 报错，是因为没设置支持ES6.最好放在JavaScript第一行

## 函数

#### 定义函数

~~~javascript
//方式一
function 函数名(){
    //代码...
}
//方式二
var 函数名() = function(){
    //代码...
}
function(){...} //这是一个匿名函数，只是将结果赋值过去
~~~

参数问题：JavaScript可以传递任意个参数，也可以不传递参数

> 假设不存在参数

~~~javascript
function(x){
    if(typeof x !== 'number'){
        throw 'Not a Number';
    }
}
~~~

> arguments 是JavaScript免费赠送的关键字
>
> 传递进来的所有参数，会是一个数组 arguments[x]来调用第x个参数

问题：arguments **包含所有的参数**，我们有时候想使用多余的参数来进行附加操作。需要排除已有参数

> rest	ES6引入的新特性

~~~javascript
//以前写法
if(arguments.length>x){
    for(var i = x;i<arguments.length;i++){
        console.log(i);	//来获得多余的参数
    }
}
//ES6之后写法
function a(x,y,...rest){//...rest 需要放在最后
    console.log(rest);	
}
~~~

#### 函数内this指向

普通函数调用 -> window

构造函数调用 -> 实例对象

对象方法调用 -> 该方法所属对象

时间绑定方法 -> 绑定事件对象

**定时器函数** -> window

立即执行函数  -> window

**箭头函数中没有的this指向的是定义处的this指向**

~~~js
// 对象没有作用域，所以在对象中的箭头函数this指向window
var obj = {
    age:18,
    say:() => {
        console.log(this.age);
    }
}
obj.say();	// undefined
~~~



#### bind方法

> 可以用来改变this指向，返回值是 **改造的原函数拷贝**
>
> 使用价值：如果函数不需要立即调用（比如定时器函数），但是希望改变这个函数内部的this指向，此时可以考虑使用bind方法

~~~js
fun.bind(thisArg,arg1,arg2......)
thisArg：在fun函数运行时指定的this值
arg1，arg2：传递的其他参数
返回由指定的this值和初始化参数改造的原函数拷贝
~~~

~~~js
// 使用方式
var o = {name:'test'};
function fn(){
    console.log(this);
};
var f = fn.bind(o);		// 此时fn函数中的this就指向了 o
f();
// 举例
var btn = document.querySelector('button');
btn.onclik = function(){
    this.disabled = true;
    setTimeout(function(){
        this.disabled = false;	// 定时器函数里面的this指向的是window
    }.bind(this),2000);			 // 使用bind() 可以使定时器函数中this指向btn
}
~~~

#### 变量的作用域

在 JavaScript中，var 定义变量实际是有作用域的。

比如在函数体中声明，则在函数体外无法使用（闭包可以实现）

**全局对象 window**

~~~javascript
var x = 'xxx';
alert(x);
alert(window.x);//默认所有的全局变量都自动绑定在window对象下
~~~

alert() 这个函数本身也是一个**`window`** 变量

JavaScript 实际上只有一个全局作用域，任何变量(函数也可以视为变量)，假设没有在函数作用范围内找到，就会向外查找，如果在全局作用域都没有找到，会报**`RefrenceError`** 错误

> 规范

由于我们所有的全局变量都会绑定到window上。如果不同的js文件，使用了相同的全局变量。则会有冲突，为了避免这种冲突，我们自己定义唯一全局变量

~~~javascript
//唯一全局变量
var 全局名字 = {所有的全局变量都放在这个对象里};
//定义全局变量
全局名字.name = 'nihao';
~~~

**局部作用域 let**

~~~javascript
function a(){
    for(var i;i<10;i++){
    //for(let i;i<10;i++){	ES6之后局部变量最好使用let
        console.log(i);
    }
    console.log(i);	//i出了作用域还可以使用(使用var定义的i)
}

~~~

**常量 const**

~~~javascript
var PI = 3.14;//在ES6之前，使用大写定义常量。这样不规范，常量会被随意改变
const PI = 3.14;//在ES6之后，使用const关键字。这样就变成了只读
~~~

#### 匿名函数自调用

~~~js
// 匿名函数自调用的上一句必须加 ; 或者在匿名函数前加
;(function(){
    console.log("test");
})()
~~~

#### 方法

方法就是把函数放在对象里面。对象只有两个东西：属性和方法

~~~javascript
//第一种写法
var aaa = {
    name:'nihao';
    birth:2000;
    //方法
    a1:function(){
        console.log(this.name);
    }
}
aaa.name	//调属性
aaa.a1()//调方法，一定要带()
//第二种写法
function getName(){
    console.log(this.name);
}
var aaa = {
    name:'nihao';
    birth:2000;
    a1:getName	//只写名字就可以
}
~~~

this正常是无法指向的，是默认指向调用它的那个对象

**apply**

JavaScript 中可以控制this的指向

~~~javascript
getName()			//取不到
getName.apply(aaa,[]);	//相当于aaa.a1()
//apply(想指向的对象,[传递的参数列表])
~~~

#### 原型（prototype）

~~~bash
显示原型与隐式原型

原型链

instanceof

执行上下文与执行上下文栈

作用域与作用域链
~~~

有时间在看吧

[尚硅谷JavaScript高级教程(javascript实战进阶)_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV14s411E7qf?p=15)

#### 闭包

> 一个函数内部的局部变量被一个新函数调用

~~~js
function f1(){
    var a = 10;
    function f2(){
        console.log(a)
    }
    return f2;
}
var f = f1();
f();
f = null;	//	让内部函数成为垃圾对象->回收闭包
~~~

两个条件：

1. 函数嵌套
2. 外部函数的局部变量被内部函数引用

**作用：**

- 使用函数内部的变量在函数执行完后，仍然存活在内存中（延长了局部变量的生命周期）
- 让函数外部可以操作到函数内部的数据（变量/函数）

**缺点：**		（能不用就不用或者及时释放来避免闭包的缺点）

- 函数执行完后，函数内的局部变量没有释放，占用内存时间会变长
- 容易造成内存泄露（内存被占用也不用）

## Promise

> Promise 是ES6引入的异步编程的新解决方案，是一个构造函数，用来封装异步操作并可与获取成功或失败的结果

~~~js
Promise 构造函数：Promise(excutor){}
Promise.prototype.then 方法
Promise.prototype.catch方法
~~~

~~~js
// 实例化 Promise 对象
const p = new Promise(function(t1,t2){
    let data = "test";
    t1(data);								 // t1是指的成功的	会走.then(x1,x2) function(value){......}第一个x1
    // t2(data); 							// t2是失败			  会走.then(x1,x2) function(e){......}       第二个x2
});
// 调用promise对象 then 方法
p.then(function(value){
    console.log(value);
},function(e){
    console.log(e);
})
// catch 只是调用失败的方法
p.catch(function(value){
    console.log(value);
})
~~~

## 内部对象

#### Date

~~~javascript
var now = new Date();
now.toLocaleString();	//'年/月/日 时:分:秒'
~~~

#### JSON

- 是一种轻量级的数据交换格式
- 简洁和清晰的**层次结构**
- 易于阅读和编写，同时也易于机器解析和生成，有效提升网络传输效率

JSON 字符串和 JS 对象的转化

~~~javascript
var user = {
    name:'nihao',
    age:3,
    sex:'男'
}
//对象转化为 json 字符串
var jsonUser = JSON.stringify(user);
//json 字符串转化为对象
var obj = JSON.parse('JSON 字符串')
~~~

JSON 和 JS 对象的区别

~~~javascript
var obj = {a:'hello',b:'nihao'};		//JS对象
var json = '{"a":"hello","b":"nihao"}'; //JSON字符串
~~~

## 面向对象编程

- 类：模板
- 对象：具体的实例

JavaScript 有些区别

JavaScript 中的**`new`**是调用构造函数

**原型：**

每一个JavaScript对象(除null外)创建的时候，就会与之关联另一个对象，这个对象就是常说的原型，每个对象都从原型中”继承“属性

所谓的继承不过就是将一个对象的原型指向了另一个对象

~~~javascript
var student = {
    name:'s1',
    age:3
    run:function(){
        console.log("run...")
    }
}
var xiaoming = {
    name:'xiaoming'
}
//原型概念
xiaoming.__proto__ = student;//xiaoming的原型指向了student，仿佛就是从student继承下来的
~~~

**class **

> ES6 新引入的

- 定义一个类，属性，方法

~~~javascript
class Student{
    constructor(name){	//构造方法
        this.name = name
    }
    hello(){
        console.log('hello');
    }
}
var xiaoming = new Student("xiaoming");	//实例类
xiaoming.hello();
~~~

- 继承

~~~javascript
class XiaoMing extends Student{}	//继承
~~~

本质：还是对象原型

**原型链**

每一个对象都会调用构造函数，构造函数又会指向实例原型，实例原型最终会指向Object.prototype(object的实例原型).

## 操作BOM对象

> BOM (Browser Object Model)  : 浏览器对象模型

 **window**

> window 代表浏览器窗口

 **location**

> 代表当前浏览器信息

~~~javascript
host:""
href:""
protocol:""	//使用的协议
reload:reload()//刷新网页
assign('path')//设置新的地址
~~~

 **document**

> document 代表当前的页面

能获取具体的文档树节点



能直接获取cookie

~~~javascript
document.cookie;
~~~

由于html中引入第三方的JavaScript代码是允许的，所以如果存在恶意代码，用户信息会泄露。为了解决这个问题，服务器在设置cookie时可以使用**`httpOnly`**.这么设定的cookie将不能被JavaScript读取

**history**	(不建议使用了)

> 代表浏览器的历史记录

~~~javascript
history.forward();	//前进
history.back();		//后退
~~~

## 操作DOM对象

> DOM(Document Object Model) ：文档对象模型

HTML文档被浏览器解析后是一棵DOM树.

#### 原生js加载完页面

~~~javascript
document.onreadystatechange = function(){
    if(document.readyState == "complete"){
        //代码...
    }
}
~~~

#### 获得DOM节点

> 第一种方法

~~~javascript
document.get······('');//获得某个节点
document.get······('')[数字].children;//获得某个节点的所有孩子节点
document.get······('')[数字].parentElement;//获得父节点
~~~

~~~javascript
document.getElementById();//因为id选择器唯一，所以可以直接定位到唯一的一个DOM节点
document.getElementsByTagName();document.getElementsByClassName();是返回一组DOM节点
~~~

> 第二种方法是使用`querySelector()`和`querySelectorAll()`，需要了解selector语法，然后使用条件来获取节点，更加方便：

~~~javascript
// 通过querySelector获取ID为q1的节点：
var q1 = document.querySelector('#q1');

// 通过querySelectorAll获取q1节点内的符合条件的所有节点：
var q2 = q1.querySelectorAll('.c');
~~~

#### 更新节点

~~~javascript
document.getElementsByClassName('a1');
a1.innerText = 'sdf';	//修改文本的值
a1.innerHTML = '<strong>123</strong>'//可以解析html文本标签

a1.style.color = 'red';//操作css样式
~~~

#### 删除节点

> 删除节点的步骤：先获取父节点，在通过父节点删除自己

~~~javascript
father.removeChild(father.children[0]);
~~~

**注意：删除多个节点的时候，children是在时刻变化的，删除节点的时候一定要注意**

#### 插入节点

获取某个Dom节点，假设这个dom节点是空的，可以通过innerHTML就可以增加一个元素了，但是这个DOM节点已经存在元素，这样会产生覆盖

**追加**

~~~html
<p id='p1'>D1</p>
<div id ='d1'>div1
    <p id='p2'>p1</p>
    <p id='p3'>p1</p>
</div>
<script>
	var a1 = document.getElementById('p1');
    var a2 = document.getElementById('d1');
    a2.appendChild(a1);//dom a1追加到a2里面
</script>
<!--追加之后代码变成-->
<div id ='d1'>div1
    <p id='p2'>p1</p>
    <p id='p3'>p1</p>
    <p id='p1'>D1</p>
</div>
~~~

**insertBefore**

~~~javascript
//子节点会插入到  referenceElement  之前
insertBefore(newElement,referenceElement);
~~~

#### 创建节点

~~~html
<script>
	var newP = document.createElement('p');//创建一个p标签
    newP.id = 'newp';	//給创建的标签赋一个id = "newp"
   	newP.setAttribute('id','newP');//newP.id = 'newp';
    newP.innerText = 'hello world!';
   	//相当于 <p id ="newp">hello world!</p>
</script>
~~~

setAttribute()方法添加指定的属性，并为其赋指定的值。如果这个指定的属性已存在，则仅设置/更改值

## 操作表单

表单的目的：提交信息

~~~javascript
var input_text = document.get···('');//获取输入框的节点
input_text.value;		//得到输入框的值
input_text.value = 123; //修改输入框的值
//对于单选框，多选框等固定的值 input_text.value只能取当前值
input_text.checked;	//查看返回结果是否为true，如果是true则被选中
input_text.checked = true;//赋值
~~~

#### 提交表单

方法一通过< form>元素的submit() 方法提交

~~~html
<form>
    <input type="text" name="test">
    <!--onlick 相应事件-->
    <button type="button" onclick="checkForm()">t</button>
</form>
<script>
	function checkForm(){
        //...
    }
</script>
~~~

方法二通过< form>本身的 onsubmit 事件

~~~html
<form onsubmit="return checkForm()">
    <input type="text" name="test">
    <button type="button">t</button>
</form>
<script>
	function checkForm(){
        //...
        return true;
    }
</script>
~~~

> 用return true来告诉浏览器继续提交，如果return false，浏览器将不会继续提交form。通常对应用户输入有误，提示用户错误信息后终止提交

#### 加密不想改变用户输入

在登录表单时，用户输入的用户名和密码处于安全考虑不传输明文，而是采用MD5加密。普通会直接修改< input>

~~~html
<form onsubmit="return checkForm()">
    <input type="text" name="username" id="username">
    <input type="password" name="password" id="password">
    <button type="button">t</button>
</form>
<script>
	function checkForm(){
		var pwd = document.getElementById('password');
        pwd.value.toMD5(pwd.value);//明文通过MD5加密
        //...
        return true;
    }
</script>
~~~

这个做法看上去没啥问题，但用户输入了口令提交时，口令框的显示会突然从几个`*`变成32个`*`（因为MD5有32个字符）。如果想不改变用户的输入，可以利用一个隐藏的输入框来实现

~~~html
<form onsubmit="return checkForm()">
    <input type="text" name="username" id="username">
    <input type="password" id="input-password">
    <input type="hidden" name="password" id="md5-password">
    <button type="button">t</button>
</form>
<script>
	function checkForm(){
		var input_pwd = document.getElementById('input-password');
        var md5_pwd = document.getElementById('md5-password')
        md5_pwd.value.toMD5(input_pwd.value);//明文通过MD5加密
        //...
        return true;
    }
</script>
~~~

## 正则

test() 正则对象方法，用于检测字符串是否符合该规则，参数是测试字符串

1. 通过调用RegExp对象的构造函数创建

~~~js
var reg = new RegExp(/表达式/);
reg,test("待测试的字符串")
~~~

2. 通过字面量创建

~~~js
var reg = /表达式/;
reg,test("待测试的字符串")
~~~

**replace()替换**

> replace() 方法可以实现替换字符串操作，用来替换的参数可以是一个字符串或一个正则表达式，返回值是一个杯替换完成的新字符串

~~~js
stringObject.replace("被替换的str","替换为str1");
~~~

~~~js
var text = "aabbaa";
var text = text.replace(/aa/,'xx' );	 // 此时的text 只能替换第一个，想要全部替换需要使用正则表达式中参数
~~~

- g：全局匹配 
- i：忽略大小写
- gi：全局匹配+忽略大小写

~~~js
var text = "aabbaa";
var text = text.replace(/aa/g,'xx' );	// 此时就可以全局替换
~~~

## jQuery

jQuery库，里面存在大量的JavaScript函数

#### 文档

**[jQuery API 中文文档 | jQuery API 中文在线手册 | jquery api 下载 | jquery api chm](https://jquery.cuishifeng.cn/index.html)**

- 一种是去官网下载[jQuery](https://jquery.com/)文件

~~~html
<script src="./jquery-3.6.0.js"></script>
~~~

- 第二种是百度搜索CDN在线库

> 公式：$(selector).action()

#### **事件**

~~~html
$(selector).action('事件函数');	//action()做的事情就叫做事件
<div id="d1"> </div>
<script>
    $('#d1').mousedown(function(){
    console.log("1a")});
</script>
~~~

#### 操作DOM

~~~JavaScript
//原生js
document.getElementById('a1');
a1.innerText = 'sdf';	//修改文本的值
a1.innerHTML = '<strong>123</strong>'//可以解析html文本标签
a1.innerHTMl = 'div{background-color:red}';//操作样式
a1.style.color = 'red';//操作css样式
~~~

~~~javascript
//使用jQu
$('#a1').text();			//获得值
$('#a1').text('sdf');		//设置值
$('#a1').html('<strong>123</strong>');	//设置html
$('#a1').html('div{background-color:red}')
$('#a1').css({"color","red"});	//设置css
~~~

元素的显示和隐藏 **本质是display:none;**

~~~javascript
$('selector').show();	//显示
$('selector').hide();	//隐藏
~~~
