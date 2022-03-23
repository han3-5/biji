## 第一个vue-cli 程序

**main.js**

~~~js
import Vue from 'vue'
import App from './App.vue'
Vue.config.productionTip = false

new Vue({
    el: "#app",
    render: h => h(App)
})
~~~

**App.vue**

~~~vue
<template>
    <div>
        <School></School>
    </div>
</template>
<script>
    import School from './components/School.vue'
    export default {
        name: 'App',
        components: {School}
    }
</script>
~~~

**components/School.vue**

~~~vue
<template>
<div>
    <h3>学校名称：{{name}}</h3>
    <h3>学校地址：{{address}}</h3>
    </div>
</template>
<script>
    export default {
        //// eslint-disable-next-line vue/multi-word-component-names
        name: 'School',
        data() {
            return {
                name:'名字',
                address:'dizhi'
            }
        },
    }
</script>
~~~

#### 注意点：

可能会报一个错误：error  Component name "School" should always be multi-word  vue/multi-word-component-names

1. 需要在School的name上面加上（第一种方法）

~~~js
// eslint-disable-next-line vue/multi-word-component-names
~~~

2.  在vue.config.js 加上关闭语法检测（第二种方法）

~~~js
lintOnSave:false  // 关闭语法检测
~~~

## 配置分析

#### render

~~~js
import Vue from 'vue'
new Vue({
  // render: h => h(App), 原貌如下
  render: function(h){
    return h('标签名','内容')
  },
}).$mount('#app')
~~~

1. vue.js 和vue.runtime.xxx.js 的区别：
    1.   vue.js是完整版的Vue。包含：核心功能+模板解析器
    2.   vue.runtime.xxx.js是运行版的Vue。只包含：核心功能
2. 因为vue.runtime.xxx.js没有模板解析器，所有不能使用template配置项，需要使用**render**函数接收到的xx(上面是h)去指定具体内容

#### 修改配置

> 因为vue把配置文件都隐藏了，所以需要使用`vue inspect > xx.js` 把配置打印出来

需要使用vue.config.js 可以对脚手架进行个性化定制[配置参考 | Vue CLI ](https://cli.vuejs.org/zh/config/)

## ref="" ($refs)

ref属性：

1. 被用来給元素或者子组件注册引用信息（id的替代者）

2. 应用在html标签上获取的是真实DOM元素，应用在组件标签上是组件实例对象

3. 使用方式：

    打标识：<h 1 ref="xxx">...</h 1>		获取：this.$refs.xxx

~~~vue
<template>
<div>
    <h1 ref="title">nihao</h1>
    <button @click="show()">点我获取</button>
    <school ref="bth"></school>
    </div>
</template>

<script>
    import School from './components/School.vue'
    export default {
        name: 'App',
        components: {School},
        methods: {
            show(){
                // console.log(document.getElementById('xx'));
                console.log(this.$refs.title);   // 真实DOM元素,类似于上方
                console.log(this.$refs.bth);    // 获得是组件的实例对象
            }
        },
    }
</script>
~~~

## 组件props

父传子

~~~vue
<school name="name1"></school>			<!--接收的是字符串-->
<school v-bind:name="name1"></school>	<!--会进行绑定，接收的会是表达式-->
<script> // school 组件的配置
export default {
    name: 'School',
    data() {
        return {
            address:'dizhi'
        }
    },
    props: ['name']  		// 第一种：只接收
    // props:{              // 第二种： 限制类型
    //     name:String
    // }
    // props:{				// 第三种：限制类型、限制必要性、指定默认值
    //     name:{
    //         type:String, //类型
    //         required:true,//必要性
    //         default:'nihao'//默认值
    //     }
    // }
}
</script>
~~~

功能：让组件接收外部传过来的数据

1. 传递数据<Demo name="xxx"/>
2. 接受数据：
    1. 第一种（只接收）：props:['name']	（常用）
    2. 第二种（限制类型）：props:{name:String}
    3. 第三种（限制类型、限制必要性、指定默认值）：props:{name:{type:xxx,required:xxx,default:xxx}}
3. props是只读的，Vue底层会检测对props的修改，如果进行了修改就会发出警告。如果数据必须要修改，就复制一份props的内容到data中，然后修改data中的数据。如：

~~~vue
<script>
export default {
    name: 'School',
    data() {
        return {
            address:'dizhi',
            myname:this.name
        }
    },
     props: ['name']  // 第一种：只接收
}
</script>
~~~

## mixin混入

school.vue

~~~vue
<template>
<div>
    <h3 @click="show()">学校名称：{{name}}</h3>
    <h3 @click="show1()">学校地址：{{address}}</h3>
    </div>
</template>
<script>
    import {hunhe,hunhe2} from '../mixin'
    export default {
        name: 'School',
        data() {
            return {
                name:'mignzi',
                address:'dizhi',
            }
        },
        mixins: [hunhe,hunhe2]	// 局部混入
    }
</script>
~~~

mixin.js

~~~js
export const hunhe = {
    methods: {
        show(){
            alert(this.name)
        }
    },
}
export const hunhe2 = {
    methods: {
        show1(){
            alert(this.address)
        }
    },
}
~~~

功能：可以吧多个组件共用的配置提取成一个混入对象

**混入如果和组件的data或methods重合了，按照组件的data或methods为准，别的会都执行**

使用方法：

1. 第一步定义混合
2. 第二步使用混入
    1. 局部混入：mixins:[xxx]
    2. 全局混入：Vue.mixin(xxx)   

## 插件

~~~js
// 对象.install = function(Vue,options)
export default{
    install(){
        alert("nihaoa!")
    }
}
~~~

main.js

~~~js
import Vue from 'vue'
import App from './App.vue'
// 引入插件
import plugins from './plugins'
Vue.config.productionTip = false

// 应用插件
Vue.use(plugins)
new Vue({
    el: "#app",
    render: h => h(App)
})
~~~

功能：用于增强Vue

本质：包含了install方法的一个对象，install的第一个参数是Vue，之后的参数是插件使用者传递的数据

使用：Vue.use()

## scoped 样式

~~~vue
<style scoped>

</style>
~~~

作用：让样式在局部生效，防止冲突

写法：< style scoped>

## Todo-list 案例

[Todo-list案例]:./Todo-list案例.md

