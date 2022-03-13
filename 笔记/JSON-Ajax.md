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

- 可以通过前端的 < iframe>  来伪造一个Ajax
- Ajax的核心是 XMLHttpRequest 对象(XHR)，XHR为 向服务器发送请求和解析服务器响应提供了接口，能以异步方式从服务器获取新数据

#### jQuery.ajax

- jQuery 提供多个与 ajax 有关的方法
- jQuery Ajax 本质就是 XMLHttpRequest，对其进行了封装

~~~js
jQuery.ajax(...)
      部分参数：
            url：请求地址
            type：请求方式，GET、POST（1.9.0之后用method）
        headers：请求头
            data：要发送的数据
    contentType：即将发送信息至服务器的内容编码类型(默认: "application/x-www-form-urlencoded; charset=UTF-8")
          async：是否异步
        timeout：设置请求超时时间（毫秒）
      beforeSend：发送请求前执行的函数(全局)
        complete：完成之后执行的回调函数(全局)
        success：成功之后执行的回调函数(全局)
          error：失败之后执行的回调函数(全局)
        accepts：通过请求头发送给服务器，告诉服务器当前客户端可接受的数据类型
        dataType：将服务器端返回的数据转换成指定类型
          "xml": 将服务器端返回的内容转换成xml格式
          "text": 将服务器端返回的内容转换成普通文本格式
          "html": 将服务器端返回的内容转换成普通文本格式，在插入DOM中时，如果包含JavaScript标签，则会尝试去执行。
        "script": 尝试将返回值当作JavaScript去执行，然后再将服务器端返回的内容转换成普通文本格式
          "json": 将服务器端返回的内容转换成相应的JavaScript对象
        "jsonp": JSONP 格式使用 JSONP 形式调用函数时，如 "myurl?callback=?" jQuery 将自动替换 ? 为正确的函数名，以执行回调函数
~~~

~~~js
function a1(){
    $.ajax({
        url:"${pageContext.request.contextPath}/a1",
        data:{'name':$("#txtName").val()},
        success:function (data,status) {
            alert(data);
            alert(status);
        }
    });
}
用户名:<input type="text" id="txtName" onblur="a1()"/>
~~~
