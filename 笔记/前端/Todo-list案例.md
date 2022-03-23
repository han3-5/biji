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

