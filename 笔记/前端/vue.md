## JavaScript框架

- jQuery
- Angular  特点：将后台的MVC模式搬到了前端，增加了模块化开发
- React ：提出了 虚拟DOM，用于减少真实DOM操作，在内存中模拟DOM操作，提升了前端渲染效率，但较为复杂
- Vue：一款渐进式JavaScript框架，所谓渐进式，就是逐步实现新特性的意思。实现模块化开发、路由、状态管理等新特性。综合了Angular(模块化)和React(虚拟DOM)的优点
- Axios：前端通信框架；因为Vue边界明确，就是为了处理DOM，所以不具备通信能力。当然也可以使用jQuery提供的AJAX

## UI框架

- Ant-Design：阿里巴巴出品，基于React的UI框架
- ElementUI、iview、ice：饿了么出品，基于Vue的UI框架
- Bootstrap：Twitter推出用于前端开发的开源工具包
- AmazeUI：又叫 "妹子UI" ，一款HTML5跨屏前端框架 

## MVVM

- Model 是指数据模型
- View 是视图层，用户界面，主要HTML和CSS来构建
- ViewModel 对后端取得的Model 数据进行处理，双向绑定

## vue 基础

> vue 就是一个MVVM的实现者，核心就是实现了DOM监听和数据绑定

#### 第一个vue

~~~html
<div id="app">
    {{message}}
</div>
<!-- 导入 Vue.js -->
<script src="https://unpkg.com/vue@2.5.21"></script>
<script>
    var vm = new Vue({
        el:"#app",
        data:{
            message:"hello vue"
        }
    })
</script>
~~~

#### 指令

> `v-xxx` 就是一个指令

**v-bind** 主要用于属性绑定	可以使用简写"v-bind:xxx"==>":xxx"

~~~html
<div id="app">
    <span v-bind:title="message">鼠标悬停···</span>
</div>
<!-- 导入 Vue.js -->
<script src="https://unpkg.com/vue@2.5.21"></script>
<script>
    var vm = new Vue({
        el:"#app",
        data:{
            message: "hello vue"
        }
    })
</script>
~~~

**v-if**

~~~html
<div id="app">
    <h1 v-if="message==='1'">1</h1>
    <h1 v-else-if="message==='2'">2</h1>
    <h1 v-else>no</h1>
</div>
<!-- 导入 Vue.js -->
<script src="https://unpkg.com/vue@2.5.21"></script>
<script>
    var vm = new Vue({
        el:"#app",
        data:{
            message: "1"
        }
    })
</script>
~~~

**v-for**

~~~html
<div id="app">
    <h1 v-for="item in items">{{item.message}}</h1>
</div>
<!-- 导入 Vue.js -->
<script src="https://unpkg.com/vue@2.5.21"></script>
<script>
    var vm = new Vue({
        el:"#app",
        data:{
            items: [
                {message:"yi"},
                {message:"er"},
                {message:"san"}
            ]
        }
    })
</script>
~~~

#### 事件

**v-on**     可以使用简写 "v-on:xxx"==>"@xxxx"

~~~html
<div id="app">
    <!--click就是一个点击事件-->
    <button v-on:click="sayHi">chilk me</button>
    <button v-on:click="saybb">chilk me</button>
</div>
<!-- 导入 Vue.js -->
<script src="https://unpkg.com/vue@2.5.21"></script>
<script>
    var vm = new Vue({
        el:"#app",
        data:{
            message: "hello vue"
        },
        methods: {  // 方法必须定义在Vue的methods中
            sayHi: function(){
                console.log(this.message);
            },
            saybb: function(){
                console.log("baibai");
            }
        }
    })
</script>
~~~

#### 双向绑定

**v-model**

~~~html
<div id="app">
    <input type="text" v-model="message">{{message}}
</div>
<!-- 导入 Vue.js -->
<script src="https://unpkg.com/vue@2.5.21"></script>
<script>
    var vm = new Vue({
        el:"#app",
        data:{
            message: ""
        }
    });
</script>
~~~

#### 组件

~~~html
<div id="app">
    <mycomponent v-for="item in items" v-bind:temp="item"></mycomponent>
</div>
<!-- 导入 Vue.js -->
<script src="https://unpkg.com/vue@2.5.21"></script>
<script>
    // 定义一个Vue组件component
    Vue.component("mycomponent",{
        props:["temp"],             // 属性传递参数
        template:"<p>{{temp}}</p>"  // 组件的模板
    });
    var vm = new Vue({
        el:"#app",
        data: {
            items: ["html","css","javascript"]
        }
    })
</script>
~~~

#### Axios 异步通信

> Axios 是一个用在浏览器端和NodeJs的异步通信框架

[axios中文文档](http://www.axios-js.com/zh-cn/docs/)

~~~json
{
    "name":"hello",
    "age":"20",
    "sex":"男"
}
~~~

~~~html
<div id="app">
    {{info.name}}
</div>
<!-- 导入 axios -->
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
<script src="https://unpkg.com/vue@2.5.21"></script>
<script>
    var vm = new Vue({
        el:"#app",
        // data:{} 属性  data(){} 方法
        data:{
            info:{
            }
        },
        mounted() {  // 钩子函数  .get 表示什么方式提交请求 .then 表示提交请求后干什么
            axios.get('data.json').then((response=>{
                this.info = response.data
            }))
        }
    })
</script>
~~~

可能会出现` Cross origin requests are only supported for protocol schemes`这种错误，需要使用服务器，VS Code可以使用Live Service插件

#### 计算属性

计算属性的重点在`属性`(计算是个动词,属性是个名词)这里的`计算`就是个函数；简单的说，**`计算属性`** 是一个将计算结果缓存起来的属性，可以想象成**缓存**

~~~html
<div id="app">
    <p>currentTime1:{{currentTime1()}}</p>
    <p>currentTime2:{{currentTime2}}</p>
</div>
<!-- 导入 Vue.js -->
<script src="https://unpkg.com/vue@2.5.21"></script>
<script>
    var vm = new Vue({
        el:"#app",
        methods: {
            currentTime1:function(){
                return Date.now();
            }
        },
        computed:{ // 计算属性
            currentTime2:function(){
                return Date.now();
            }
        }
    })
</script>
~~~

如果打开浏览器的 Console 可以发现，每次调用 currentTime1,值一直在改变，而调用 currentTime2,值不会变

#### 插槽

~~~html
<div id="app">
    <test>
        <title-test1 slot="title-test" v-bind:title="title"></title-test1>
        <item-test1 slot="item-test" v-for="item in items" v-bind:item="item"></item-test1>
    </test>
</div>
<!-- 导入 Vue.js -->
<script src="https://unpkg.com/vue@2.5.21"></script>
<script>
    // slot：插槽
    Vue.component("test",{
        template: ` <div>
                            <slot name="title-test"></slot>
                            <ul>
                                <slot name="item-test"></slot>
                    		</ul>
                    </div>`
    });
    Vue.component("title-test1",{
        props: ["title"],
        template: "<div>{{title}}</div>"
    });
    Vue.component("item-test1",{
        props: ["item"],
        template: "<li>{{item}}</li>"
    });
    var vm = new Vue({
        el:"#app",
        data: {
            title:"列表",
            items: ["html","css","js"]
        }
    });
</script>
~~~

注意：定义标签名字的时候，不能有大写字母，要换成"-"

#### 自定义事件分发

**this.$emit("自定义事件名",参数)**

~~~html
<div id="app">
    <test>
        <title-test1 slot="title-test" v-bind:title="title1"></title-test1>
        <!-- v-for 的index索引往后面放 -->
        <item-test1 slot="item-test" v-for="(item1,index) in items"
                    v-bind:item="item1"  v-bind:index="index" v-on:remove111="removeitems(index)"></item-test1>
    </test>
</div>
<!-- 导入 Vue.js -->
<script src="https://unpkg.com/vue@2.5.21"></script>
<script>
    // slot：插槽
    Vue.component("test",{
        template: ` <div>
                            <slot name="title-test"></slot>
                            <ul>
                                <slot name="item-test"></slot>
   							</ul>
   					 </div>`
    });
    Vue.component("title-test1",{
        props: ["title"],
        template: "<div>{{title}}</div>"
    });
    Vue.component("item-test1",{
        props: ["item","index"],
        // 只能绑定当前组件的方法
        template: "<li>{{item}}--{{index}}<button v-on:click='remove'>删除</button></li>",
        methods: {
            remove: function(index){
                // this.$emit("自定义事件名",参数)
                this.$emit("remove111",index)
            }
        },
    });
    var vm = new Vue({
        el:"#app",
        data: {
            title1:"列表",
            items: ["html","css","js"]
        },
        methods: {
            removeitems: function(index){
                console.log("删除了 "+this.items[index]);
                this.items.splice(index,1);// 一次删除一个元素
            }
        }
    });
</script>
~~~

> 难点就一个：是谁绑了我，而我又绑了谁

## vue-cli

> vue-cli 是官方提供的一个脚手架，用于快速生成一个vue的项目模板

下载

~~~bash
npm install vue-cli -g
~~~

#### 第一个 vue-cli(老)

在cmd窗口下初始化 vue-cli

~~~bash
npm i -g @vue/cli-init # 为了能使用旧版本的 vue init
vue init webpack xxxx # xxxx是创建的文件名
~~~

然后会有一些提示

~~~bash
Project name：项目名称，默认回车即可
Project description：项目描述，默认回车即可
Author：项目作者，默认回车即可
Install vue-router：是否安装vue-router，选择n不安装（后期需要再手动添加）
Use ESLint to lint your code:是否使用ESLint做代码检查，选择n不安装（后期需要再手动添加)
Set up unit tests:单元测试相关，选择n不安装（后期需要再手动添加）
Setupe2etests with Nightwatch：单元测试相关，选择n不安装（后期需要再手动添加）
Should we run npm install for you after the,project has been created:创建完成后直接初始化，选择n，我们手动执行；运行结果
~~~

初始化并运行

~~~bash
cd myvue
npm install
# 安装依赖的时候可能会出错，按照提示，运行就
npm run dev
~~~

#### 第一个 @vue/cli

[vue cli3.0快速搭建项目详解（网上） ](https://www.cnblogs.com/coober/p/10875647.html)

[vue cli3.0快速搭建项目详解（本地）](./vue cli3.0.md)

~~~bash
# npm uninstall vue-cli -g  卸载老版本
npm install -g @vue/cli	
# vue -V	查看版本
~~~

在cmd窗口下初始化 vue-cli

~~~bash
vue create xxxx # xxxx是创建的文件名
~~~



