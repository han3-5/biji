#### 1.下载依赖

~~~js
npm install mavon-editor -S
~~~

#### 2.引入组件

~~~js
// main.js全局注册
import mavonEditor from 'mavon-editor'
import 'mavon-editor/dist/css/index.css'
// use
Vue.use(mavonEditor)
~~~

#### 3.vue页面使用

~~~js
<div>
    <mavon-editor ref="md" v-model="value" :ishljs="true" @imgAdd="imgAdd"/>
</div>

<!-- 预览 -->
<mavon-editor
    class="md"
    :value="article.content"
    :subfield="false"
    :defaultOpen="'preview'"
    :toolbarsFlag="false"
    :editable="false"
    :scrollStyle="true"
    :ishljs="true"
/>

 // 绑定@imgAdd event
imgAdd(pos, $file) {
  let $vm = this.$refs.md
  // 第一步.将图片上传到服务器.
  const formData = new FormData();
  formData.append('file', $file);
  axios({
    url: 'http://localhost:9090/file/upload',
    method: 'post',
    data: formData,
    headers: {'Content-Type': 'multipart/form-data'},
  }).then((res) => {
    // 第二步.将返回的url替换到文本原位置![...](./0) -> ![...](url)
    $vm.$img2Url(pos, res.data);
  })
},
~~~

