## 静态

vue.config.js加上	 lintOnSave:false  // 关闭语法检测

main.js

~~~js
import Vue from 'vue';
import App from './App.vue'
Vue.config.productionTip = false

new Vue({
    el: "#app",
    render: h => h(App)
})
~~~

App.vue

~~~vue
<template>
<body>
    <div id="root">
        <div class="todo-container">
            <div class="todo-wrap">
                <my-header></my-header>
                <my-list></my-list>
                <my-footer></my-footer>
    </div>
    </div>
    </div>

    </body>
</template>

<script>
    import MyHeader from './components/MyHeader.vue'
    import MyList from './components/MyList.vue'
    import MyFooter from './components/MyFooter.vue'
    export default {
        name: 'App',
        components:{MyHeader,MyList,MyFooter},
    }
</script>

<style>
    /*base*/
    body {
        background: #fff;
    }

    .btn {
        display: inline-block;
        padding: 4px 12px;
        margin-bottom: 0;
        font-size: 14px;
        line-height: 20px;
        text-align: center;
        vertical-align: middle;
        cursor: pointer;
        box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.2), 0 1px 2px rgba(0, 0, 0, 0.05);
        border-radius: 4px;
    }

    .btn-danger {
        color: #fff;
        background-color: #da4f49;
        border: 1px solid #bd362f;
    }

    .btn-danger:hover {
        color: #fff;
        background-color: #bd362f;
    }

    .btn:focus {
        outline: none;
    }

    .todo-container {
        width: 600px;
        margin: 0 auto;
    }
    .todo-container .todo-wrap {
        padding: 10px;
        border: 1px solid #ddd;
        border-radius: 5px;
    }
</style>
~~~

MyHeader.vue

~~~vue
<template>
    <div class="todo-header">
        <input type="text" placeholder="请输入你的任务名称，按回车键确认"/>
    </div>
</template>

<script>
    export default {
        name: 'MyHeader'
    }
</script>

<style scoped>
/*header*/
.todo-header input {
  width: 560px;
  height: 28px;
  font-size: 14px;
  border: 1px solid #ccc;
  border-radius: 4px;
  padding: 4px 7px;
}

.todo-header input:focus {
  outline: none;
  border-color: rgba(82, 168, 236, 0.8);
  box-shadow: inset 0 1px 1px rgba(0, 0, 0, 0.075), 0 0 8px rgba(82, 168, 236, 0.6);
}
</style>
~~~

MyList.vue

~~~vue
<template>
    <ul class="todo-main">
        <my-item></my-item>
    </ul>
</template>

<script>
import MyItem from './MyItem'
    export default {
        name: 'MyList',
        components: {MyItem},
    }
</script>

<style scoped>
/*main*/
.todo-main {
  margin-left: 0px;
  border: 1px solid #ddd;
  border-radius: 2px;
  padding: 0px;
}

.todo-empty {
  height: 40px;
  line-height: 40px;
  border: 1px solid #ddd;
  border-radius: 2px;
  padding-left: 5px;
  margin-top: 10px;
}
</style>
~~~

MyItem.vue

~~~vue
<template>
<li>
    <label>
        <input type="checkbox"/>
        <span>xxxxx</span>
    </label>
    <button class="btn btn-danger" style="display:none">删除</button>
    </li>
</template>

<script>
    export default {
        name: 'MyItem',
    }
</script>

<style scoped>
    /*item*/
    li {
        list-style: none;
        height: 36px;
        line-height: 36px;
        padding: 0 5px;
        border-bottom: 1px solid #ddd;
    }

    li label {
        float: left;
        cursor: pointer;
    }

    li label li input {
        vertical-align: middle;
        margin-right: 6px;
        position: relative;
        top: -1px;
    }

    li button {
        float: right;
        display: none;
        margin-top: 3px;
    }

    li:before {
        content: initial;
    }

    li:last-child {
        border-bottom: none;
    }
</style>
~~~

MyFooter.vue

~~~vue
<template>
    <div class="todo-footer">
        <label>
            <input type="checkbox"/>
        </label>
        <span>
            <span>已完成0</span> / 全部2
        </span>
        <button class="btn btn-danger">清除已完成任务</button>
    </div>
</template>

<script>
    export default {
        name: 'MyFooter'
    }
</script>

<style scoped>
/*footer*/
.todo-footer {
  height: 40px;
  line-height: 40px;
  padding-left: 6px;
  margin-top: 5px;
}

.todo-footer label {
  display: inline-block;
  margin-right: 20px;
  cursor: pointer;
}

.todo-footer label input {
  position: relative;
  top: -1px;
  vertical-align: middle;
  margin-right: 5px;
}

.todo-footer button {
  float: right;
  margin-top: 5px;
}
</style>
~~~

## 初始化列表

Mylist.vue

~~~vue
<template>
    <ul class="todo-main">
        <my-item v-for="todoObj in todos" :key="todoObj.id" :todo="todoObj"></my-item>
    </ul>
</template>

<script>
import MyItem from './MyItem'
    export default {
        name: 'MyList',
        components: {MyItem},
        data() {
            return {
                todos: [
                    {id:"001",title:"test1",done:true},
                    {id:"002",title:"test2",done:false},
                    {id:"003",title:"test3",done:true},
                ]
            }
        },
    }
</script>
~~~

MyItem.vue

~~~vue
<template>
<li>
    <label>
        <input type="checkbox" :checked='todo.done'/>
        <span>{{todo.title}}</span>
    </label>
    <button class="btn btn-danger" style="display:none">删除</button>
    </li>
</template>

<script>
    export default {
        name: 'MyItem',
        props: ['todo']
    }
</script>
~~~

## 添加

1. 把MyList.vue中的todos移到app组件中，因为两个兄弟组件之间不好传数据（现阶段）

~~~vue
<template>
    //....
    <my-header :addTodo="addTodo"></my-header>
    <my-list :todos="todos"></my-list>
    //.....
</template>
<script>
    import MyHeader from './components/MyHeader.vue'
    import MyList from './components/MyList.vue'
    import MyFooter from './components/MyFooter.vue'
    export default {
        name: 'App',
        components:{MyHeader,MyList,MyFooter},
        data() {
            return {
                todos: [
                    {id:"001",title:"test1",done:true},
                    {id:"002",title:"test2",done:false},
                    {id:"003",title:"test3",done:true},
                ]
            }
        },
        methods: {
            addTodo(todoObj){
                this.todos.unshift(todoObj);
            }
        },
    }
</script>
~~~

MyList.vue

~~~vue
<script>
import MyItem from './MyItem'
    export default {
        name: 'MyList',
        components: {MyItem},
        props: ['todos']
    }
</script>
~~~

MyHeader.vue

~~~bash
npm i nanoid # 小型版的uuid
~~~

~~~vue
<template>
    <div class="todo-header">
        <input type="text" placeholder="请输入你的任务名称，按回车键确认" v-model="title" @keyup.enter="add"/>
    </div>
</template>

<script>
    import {nanoid} from 'nanoid'
    export default {
        name: 'MyHeader',
        data() {
            return {
                title:''
            }
        },
        methods: {
            add(){
                // 校验数据
                if(this.title.trim() === ''){
                    alert('输入不能为空')
                    return
                }
                // 将用户输入包装成一个todo对象
                const todoobj = {id:nanoid(),title:this.title,done:false};
                // 通知App组件添加一个todo对象
                this.addTodo(todoobj);
                this.title= ''; // 清空输入
            }
        },
        props: ['addTodo']
    }
</script>
~~~

## 勾选

- 不建议的方法     v-model

~~~vue
<!--MyItem.vue-->
<input type="checkbox" v-model='todo.done'/>
~~~

因为todo是props接受过来的对象，而props是只读，不能修改，但Vue只是检测内存地址，所以更改对象中的一些属性不会引起报错。如果更改的是对象，则会报错

App.vue

~~~vue
<template>
  <body>
<div id="root">
    <div class="todo-container">
        <div class="todo-wrap">
            <my-header :addTodo="addTodo"></my-header>
            <my-list :todos="todos" :checkTodo="checkTodo"></my-list>
            <my-footer></my-footer>
        </div>
    </div>
</div>

</body>
</template>

<script>
    // 代码...
    export default {
        // 代码...
        methods: {
            // 代码....
            checkTodo(id){
                this.todos.forEach(todo => {
                    if(todo.id === id){
                        todo.done = !todo.done
                    }
                }
</script>
~~~

MyList.vue		需要爷传给父再传给字

~~~vue
<my-item v-for="todoObj in todos" :key="todoObj.id" :todo="todoObj" :checkTodo="checkTodo"></my-item>
~~~

MyItem.vue

~~~vue
<template>
<li>
    <label>
        <input type="checkbox" :checked='todo.done' @click="handleCheck(todo.id)"/>
        <span>{{todo.title}}</span>
    </label>
    <button class="btn btn-danger" style="display:none">删除</button>
    </li>
</template>

<script>
    export default {
        name: 'MyItem',
        props: ['todo','checkTodo'],
        methods: {
            handleCheck(id){
                this.checkTodo(id)
            }
        },
    }
</script>
~~~

## 删除

- Myitem 更改两个css

~~~css
li:hover{
  background-color: #ddd;
}
li:hover button{
  display: block;
}
~~~

其他和勾选一样，

App.vue

~~~js
// 删除一个todo
deleteTodo(id){
    this.todos = this.todos.filter((todo)=>{
        return todo.id !== id
    })
}
~~~

MyItem.vue

~~~vue
handleDelete(id){
    if(confirm('确定删除吗？')){
    	this.deleteTodo(id)
    }
}
~~~

## 底部统计

App.vue 吧 todos传给MyFooter

MyFooter

~~~vue
<template>
<div class="todo-footer">
    <label>
        <input type="checkbox"/>
    </label>
    <span>
        <span>已完成{{todoDone}}</span> / 全部{{this.todos.length}}
    </span>
    <button class="btn btn-danger">清除已完成任务</button>
    </div>
</template>

<script>
    export default {
        name: 'MyFooter',
        props: ['todos'],
        computed: {
            todoDone(){
                let i = 0;
                this.todos.forEach(todo => {
                    if(todo.done){
                        i++;
                    }
                });
                return i;
            }
        }
    }
</script>
~~~

## 底部交互

App.vue

~~~vue
<template>
  <body>
<div id="root">
    .....
    <my-footer :todos="todos" :checkAllTodo="checkAllTodo" :clearAllTodo="clearAllTodo"></my-footer>
    .....
</template>

<script>
    		// 代码...
    export default {
			// 代码...
        methods: {
			//代码...
            
          // 全选or取消全选
          checkAllTodo(done){
            this.todos.forEach((todo)=>{
              todo.done = done
            })
          },
          // 清除所有已完成
          clearAllTodo(){
            this.todos = this.todos.filter((todo)=>{
              return !todo.done
            })
          }
        },
    }
</script>
~~~

MyFooter.vue

~~~vue
<template>
<div class="todo-footer" v-show="todos.length">
    <label>
        <!-- <input type="checkbox" :checked="isAll" @click="checkAll"/> -->
        <input type="checkbox" v-model="isAll"/>
    </label>
    <span>
        <span>已完成{{todoDone}}</span> / 全部{{todos.length}}
    </span>
    <button class="btn btn-danger" @click="clearAll">清除已完成任务</button>
    </div>
</template>

<script>
    export default {
        name: 'MyFooter',
        props: ['todos','checkAllTodo','clearAllTodo'],
        computed: {
            todoDone(){
                let i = 0;
                this.todos.forEach(todo => {
                    if(todo.done){
                        i++;
                    }
                });
                return i;
            },
            // isAll(){
            //   return this.todoDone === this.todos.length && this.todos.length > 0
            // }	计算属性的简略写法
            isAll:{
                get(){
                    return this.todoDone === this.todos.length && this.todos.length > 0
                },
                set(value){
                    this.checkAllTodo(value)
                }
            }
        },
        methods: {
            checkAll(e){
                this.checkAllTodo(e.target.checked)
            },
            clearAll(){
                this.clearAllTodo()
            }
        },
    }
</script>
~~~

## 总结

1. 组件化编码流程
    1. 拆分静态组件：组件要按照功能点拆分，命名不要和html元素冲突
    2. 实现动态组件：考虑好数据的存放位置，数据是一个组件再用，还是一些组件再用：
        1. 一个组件再用：放在组件自身即可
        2. 一些组件再用：放在他们的共同的父组件上
    3. 实现交互：从绑定事件开始
2. props使用于：
    1. 父组件 ==> 子组件	通信
    2. 子组件 ==> 父组件    通信（要求父先给子一个函数）
3. 使用 v-model 时要切记：v-model绑定的值不能是props传过来的值，因为props是不可以修改的
4. props传过来的若是对象类型的值，修改对象中的属性时Vue不会报错，但不要这么这么做
