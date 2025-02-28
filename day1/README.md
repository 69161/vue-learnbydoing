### 项目驱动学习 - 正反馈


#### 项目初始化 

```
npm create vite@latest vue-learnbydoing -- --template vue 
// 第一个 -- 表示后续参数传递给vite
// --template vue 指定使用Vue模板，其他模板如React
cd vue-learnbydoing
npm install
npm run dev
```

##### 项目目录

![3dSX5xt.jpg](https://iili.io/3dSX5xt.jpg)

##### 目录结构

```bash
node_modules # npm install 
public # 必须保留原始文件名/路径的特殊资源存放  根路径 /vite.svg 直接访问
src # 99%的工作在此
src/assets # 98%的静态资源存放 相对路径 @/assets/vue.svg
src/components # 通用组件库 高复用性 无业务逻辑
src/App.vue # 根组件
src/main.js
src/style.css
index.html 
```

##### index.html 文件

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/vite.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Vite + Vue</title>
  </head>
  <body>
    <div id="app"></div>
    <script type="module" src="/src/main.js"></script>
  </body>
</html>

// <link ... href="/vite.svg"/> 引用的public里的svg 根路径直接访问
// <div id="app"></div> div通过id属性可作为div元素唯一的标识符
// <script type="module" src="/src/main.js"></script> 
// module是script属性，指定脚本类型；调用的/src/main.js
```

##### /src/main.js

```js
// 从 vue npm 包中导入createApp函数
import { createApp } from 'vue'
// 导入相对路径下style.css 所以在index.html没看到style标签
import './style.css'
// 从当前目录相对路径导入App.vue单文件组件，命名为App
import App from './App.vue'
// createApp函数方法创建Vue应用实例，参数为App，后续通过.mount()挂载到html中id为app的DOM元素
createApp(App).mount('#app')
```

##### App.vue

```vue
// 基本结构 
// setup 语法糖 不再需要写setup()函数和return语句
// 1.组件初始化时自动执行script setup中的代码
// 2.import导入的组件直接在模板中使用，无需注册
// 3.所有顶层变量/函数自动暴露给模板，无需手动return
<script setup>
import HelloWorld from './components/HelloWorld.vue'
</script>
// HTML中的body
<template>
<div>
    <a href="https://vite.dev" target="_blank">
      <img src="/vite.svg" class="logo" alt="Vite logo" />
    </a>
    <a href="https://vuejs.org/" target="_blank">
      <img src="./assets/vue.svg" class="logo vue" alt="Vue logo" />
    </a>
  </div>
// Helloworld 是引入的自定义组件，属性传递，向子组件传递名为msg的prop，值为字符串“Vite + Vue"
  <HelloWorld msg="Vite + Vue" />
</template>
//scoped主要是实现CSS样式的作用域隔离，样式只会作用于当前组件模板元素
<style scoped></style>
```

##### HelloWorld.vue

```vue
<script setup>

//ref 只有一个属性.value;
//reactive 与 ref 核心区别及使用指南？一般处理对象用reactive，其他场景用ref；

import { ref } from 'vue'

// Props声明，定义组件接受的porps，声明了名为msg的prop，类型为String
// 父组件App.vue通过<HelloWorld msg=""/>传递
defineProps({
  msg: String,
})

const count = ref(0)
</script>

<template>

  // 双大括号表示文本插值
  <h1>{{ msg }}</h1>

  <div class="card">
      
     // @click 等价于v-on:click 监听事件 事件函数"count=count+1"
    <button type="button" @click="count++">count is {{ count }}</button>
    <p>
      Edit
      <code>components/HelloWorld.vue</code> to test HMR
    </p>
  </div>

  <p>
    Check out
    <a href="https://vuejs.org/guide/quick-start.html#local" target="_blank"
      >create-vue</a
    >, the official Vue + Vite starter
  </p>
  <p>
    Learn more about IDE Support for Vue in the
    <a
      href="https://vuejs.org/guide/scaling-up/tooling.html#ide-support"
      target="_blank"
      >Vue Docs Scaling up Guide</a
    >.
  </p>
  <p class="read-the-docs">Click on the Vite and Vue logos to learn more</p>
</template>
<style scoped>
.read-the-docs {
  color: #888;
}
</style>
```


