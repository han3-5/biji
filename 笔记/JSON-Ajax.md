## JSON

**JavaScript Object Notation**

在 JS 语言中，一切都是对象。因此，任何 JS 支持的类型都可以通过 JSON 来表示

要求与语法格式：

- 对象表示为键值对
- 数据由逗号分割，最后一个不加逗号
- 花括号保存对象
- 方括号保存数组

~~~json
{"name":"nihao"}
{"age":"3"}
// 与js对象的关系。JSON是js对象的字符串表示法，使用本文表示js对象的信息，本质是一个字符串
var obj = {a:'hello',b:'world'};		// 这是一个对象
var json = {"a":"hello","b":"world"};	// 这是一个Json字符串
~~~

> JSON 和 JS 对象互转

~~~json
var obj = JSON.parse('{"a":"hello","b":"world"}');	// 结果是{a:'hello',b:'world'}
var json = JSON.stringify({a:'hello',b:'world'});	// 结果是{"a":"hello","b":"world"}
~~~

#### 前后台传 Json 对象

@RespnseBody注解：把后台的对象转换为json对象，返回到页台

@RequestBody注解：负责接收前台的json数据，吧json数据自动封装到pojo中

## Ajax

**Asynchronous Javascript And XML**（异步的JavaScript和xml）

在无需重新加载整个页面的情况下，能够更新部分页面的技术

