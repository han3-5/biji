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

## vue

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

**v-bind** 主要用于属性绑定

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

**v-on**

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