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

#### java转json 转换器

1. 提供对象转换器  JacksonObjectMapper

~~~java
package com.zyh.reggie.common;

import com.fasterxml.jackson.databind.DeserializationFeature;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.module.SimpleModule;
import com.fasterxml.jackson.databind.ser.std.ToStringSerializer;
import com.fasterxml.jackson.datatype.jsr310.deser.LocalDateDeserializer;
import com.fasterxml.jackson.datatype.jsr310.deser.LocalDateTimeDeserializer;
import com.fasterxml.jackson.datatype.jsr310.deser.LocalTimeDeserializer;
import com.fasterxml.jackson.datatype.jsr310.ser.LocalDateSerializer;
import com.fasterxml.jackson.datatype.jsr310.ser.LocalDateTimeSerializer;
import com.fasterxml.jackson.datatype.jsr310.ser.LocalTimeSerializer;
import java.math.BigInteger;
import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.LocalTime;
import java.time.format.DateTimeFormatter;
import static com.fasterxml.jackson.databind.DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES;

/**
 * 对象映射器:基于jackson将Java对象转为json，或者将json转为Java对象
 * 将JSON解析为Java对象的过程称为 [从JSON反序列化Java对象]
 * 从Java对象生成JSON的过程称为 [序列化Java对象到JSON]
 */
public class JacksonObjectMapper extends ObjectMapper {

    public static final String DEFAULT_DATE_FORMAT = "yyyy-MM-dd";
    public static final String DEFAULT_DATE_TIME_FORMAT = "yyyy-MM-dd HH:mm:ss";
    public static final String DEFAULT_TIME_FORMAT = "HH:mm:ss";

    public JacksonObjectMapper() {
        super();
        //收到未知属性时不报异常
        this.configure(FAIL_ON_UNKNOWN_PROPERTIES, false);

        //反序列化时，属性不存在的兼容处理
        this.getDeserializationConfig().withoutFeatures(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES);


        SimpleModule simpleModule = new SimpleModule()
            .addDeserializer(LocalDateTime.class, new LocalDateTimeDeserializer(DateTimeFormatter.ofPattern(DEFAULT_DATE_TIME_FORMAT)))
            .addDeserializer(LocalDate.class, new LocalDateDeserializer(DateTimeFormatter.ofPattern(DEFAULT_DATE_FORMAT)))
            .addDeserializer(LocalTime.class, new LocalTimeDeserializer(DateTimeFormatter.ofPattern(DEFAULT_TIME_FORMAT)))

            .addSerializer(BigInteger.class, ToStringSerializer.instance)
            .addSerializer(Long.class, ToStringSerializer.instance)
            .addSerializer(LocalDateTime.class, new LocalDateTimeSerializer(DateTimeFormatter.ofPattern(DEFAULT_DATE_TIME_FORMAT)))
            .addSerializer(LocalDate.class, new LocalDateSerializer(DateTimeFormatter.ofPattern(DEFAULT_DATE_FORMAT)))
            .addSerializer(LocalTime.class, new LocalTimeSerializer(DateTimeFormatter.ofPattern(DEFAULT_TIME_FORMAT)));

        //注册功能模块 例如，可以添加自定义序列化器和反序列化器
        this.registerModule(simpleModule);
    }
}

~~~

2. 使用消息转换器

~~~java
package com.zyh.reggie.config;

import com.zyh.reggie.common.JacksonObjectMapper;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.converter.HttpMessageConverter;
import org.springframework.http.converter.cbor.MappingJackson2CborHttpMessageConverter;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurationSupport;

import java.util.List;

@Configuration
public class WebMvcConfig extends WebMvcConfigurationSupport {
    /**
     * 扩展mvc框架的消息转换器
     * @param converters
     */
    @Override
    protected void extendMessageConverters(List<HttpMessageConverter<?>> converters) {
        // 创建消息转换器对象
        MappingJackson2HttpMessageConverter messageConverter = new MappingJackson2HttpMessageConverter();
        // 设置对象转换器，底层使用Jackson将java对象转为json
        messageConverter.setObjectMapper(new JacksonObjectMapper());
        // 将上面的消息转换器对象追加到mvc框架的转换器集合中，并设置为优先使用
        converters.add(0,messageConverter);
    }
}
~~~



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

#### from表单数据提交

~~~js
function a1(){
    $.ajax({
        url:"${pageContext.request.contextPath}/a1",
        data:$("#form-change-avatar").serialize(),
        success:function (data,status) {
            alert(data);
            alert(status);
        }
    });
}
~~~

- serialize():可以将表单数据自动拼接成key=value的结构进行提交给服务器
- FormData类：将表单中数据保持原有的结构进行数据的提交 只适用提交文件）

~~~js
new FormData($("#form")[0]);	// 文件类型的数据可以使用FormData对象进行存储
~~~

- ajax默认处理数据时按照字符串的形式进行处理，以及默认会采用字符串的形式进行数据提交。关闭这两个默认功能

~~~js
function a1(){
    $.ajax({
        url:"${pageContext.request.contextPath}/a1",
        data:new FormData($("#form-change-avatar")[0]),
        processData:false,// 处理数据的形式，关闭处理数据
        contentType:false,// 提交数据的形式，关闭默认提交数据的形式
        success:function (data,status) {
            alert(data);
            alert(status);
        }
    });
}
~~~

#### url小问题

~~~java
// url: '/account/edit?accountId=' + ${account.uid} ,
// 写成上面那种，el表达式后面有个 ',' 可能前端会报下面的错误
// 这个时候替换成这种试试 url: '/account/edit?accountId=' + ${account.uid} +'',
VM423:54 Uncaught SyntaxError: Unexpected token ','
    at b (jquery-3.5.0.min.js:2:839)
    at Pe (jquery-3.5.0.min.js:2:48553)
    at S.fn.init.append (jquery-3.5.0.min.js:2:49904)
    at layui.all.js:5:7688
    at s.pt.vessel (layui.all.js:5:5018)
    at s.pt.creat (layui.all.js:5:7411)
    at new s (layui.all.js:5:4190)
    at Object.r.open (layui.all.js:5:23194)
    at Object.success (all:164:27)
    at c (jquery-3.5.0.min.js:2:28294)
~~~

#### 想传一个对象一个String

~~~xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.79</version>
</dependency>

<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-core</artifactId>
    <version>2.9.5</version>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.5</version>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-annotations</artifactId>
    <version>2.9.5</version>
</dependency>
~~~

~~~js
// 前端
$.ajax({
    url: '/account/in',
    type: 'post',
    data: {
        value:JSON.stringify(value),
        acid:acid
    },
    success: function (data) {}
})
~~~

~~~java
// 后端
@PostMapping("/account/in")
@ResponseBody
public String accountIn(@RequestParam("value") String value,@RequestParam("acid") String acid){
    System.out.println("==="+acid);
    Card card = JSONObject.parseObject(value,Card.class);

}
~~~

