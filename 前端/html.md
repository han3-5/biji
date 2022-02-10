## 列表

#### 有序列表

~~~
<ol>
	<li></li>
	<li></li>
</ol>
~~~

#### 无序列表

~~~
<ul>
	<li></li>
	<li></li>
</ul>
~~~

#### 自定义列表

~~~
<dl>
	<dt></dt>
	
	<dd></dd>
	<dd></dd>
</dl>
~~~

## 表格

行：tr

列：td

~~~bash
<table>
	<tr>
		<td colspan="4"></td> #colspan 跨列
		<td rowspan="4"></td> #rowspan 跨行
	</tr>
</table>
~~~

## 媒体元素

#### 视频

~~~~bash
#controls 控制条
#autoplay 自动播放
<video src="path" controls autoplay> </video>
~~~~

#### 音频

~~~bash
#controls 控制条
#autoplay 自动播放
<audio src="path" controls autoplay> </audio>
~~~

## 表单

~~~
<form method="提交方式" action="提交位置">
	<input name="" type=""/>
</form>
~~~

input 的 一些属性 name一样表示一个组

![](./image/1.png)

> radio 			单选框						checkbox	  多选框

按钮

> button			普通按钮					image			  图像按钮
>
> submit			提交按钮					reset				重置按钮

#### 下拉框

~~~
<select name="列表名称">
	<option ></option>
	<option ></option>
</select>
~~~

#### 文本域

~~~bash
<textarea name="" cols="50" rows="10"></textarea> #50列10行
~~~

#### 初级验证

为了一些安全，及缓解压力，写在input标签里

placeholder="  "		提示信息

required					  非空判断，也就是必须要填写

pattern="正则语句"	正则表达式