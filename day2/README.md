是否曾有这样矛盾的时刻： 

在他人的教程中循迹前行时，灵感如泉涌般恣意流淌， 

却在孤身踏入实战的荒野时， 赤脚下的航标早已消散， 

独留你在代码与逻辑里辗转……

### 项目驱动学习

#### 项目一：智能待办清单

想一下哪些功能：任务创建（输入-提交）；展示任务列表；标记完成任务；删除任务；

想不出来也没关系，别告诉我你不会"问"，不会"抄"；DeepSeek走你；

```vue
<script setup>
import { ref, onMounted } from 'vue'

// 响应式数据
const todos = ref([])
const newTodo = ref('')

// 添加待办事项
const addTodo = () => {
  if (newTodo.value.trim()) {
    todos.value.push({
      id: Date.now(),
      text: newTodo.value.trim(),
      completed: false
    })
    newTodo.value = ''
  }
}

// 持久化存储
onMounted(() => {
  const saved = localStorage.getItem('todos')
  if (saved) todos.value = JSON.parse(saved)
})

// 监听todos变化
watch(todos, (newVal) => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep: true })
</script>

<template>
  <div class="container">
    <h1>智能待办清单</h1>
    <div class="input-group">
      <input 
        v-model="newTodo"
        @keyup.enter="addTodo"
        placeholder="输入待办事项..."
        class="todo-input"
      >
      <button @click="addTodo" class="add-btn">添加</button>
    </div>
    
    <ul v-if="todos.length" class="todo-list">
      <li 
        v-for="todo in todos"
        :key="todo.id"
        :class="{ completed: todo.completed }"
        class="todo-item"
      >
        <input 
          type="checkbox"
          v-model="todo.completed"
          class="toggle"
        >
        <span>{{ todo.text }}</span>
        <button 
          @click="todos = todos.filter(t => t.id !== todo.id)"
          class="delete-btn"
        >×</button>
      </li>
    </ul>
    
    <p v-else class="empty-tip">暂无待办事项，开始添加吧！</p>
  </div>
</template>

<style scoped>
/* 添加你的CSS样式 */
.container { max-width: 600px; margin: 2rem auto; }
.todo-input { flex: 1; padding: 0.8rem; }
.completed { text-decoration: line-through; opacity: 0.7; }
/* 更多样式... */
</style>
```

**警告：上述代码复制到App文件内，页面不显示，俗话说：错误就是学习进步的开始；**

控制台提示 `watch is not defined`

```vue
// 
import { ref, onMounted, watch } from 'vue'
```

逐行分析代码，最好先自己大概过一遍，不懂的就去问你的DeekSeek老师，或者Cursor老师等等

```vue	
<script setup>
	import { ref } from 'vue'
	const newTodo = ref('')
    addTodo() {
        if (newTodo.value.trim()) {
    		todos.value.push({
      			id: Date.now(),
      			text: newTodo.value.trim(),
      			completed: false
    		})
    		newTodo.value = ''
  		}
    }
</script>

<templete>
	<input 
  		v-model="newTodo" 
  		@keyup.enter="addTodo"
  		placeholder="输入待办事项..."
  		class="todo-input"
  	>
    <button @click="addTodo" class="add-btn">添加</button>
</templete>
```

`v-model`表单输入双向绑定，本质是 `v-bind:value` + `v-on:input` 语法糖

`v-bind` 语法糖  `:` 

`v-on` 语法糖 `@`

```vue
<input 
	  v-bind:value="newTodo" 
    v-on:input="value = $event.target.value"
    v-on:keyup.enter="addTodo"
    placeholder="输入待办事项..."/>
```

自我复习知识点：`e.target`  `keyup`  `keyup.enter`

```vue
<script setup>
	import { ref } from 'vue'
    const newTodo = ref('')
</script>
```

**`ref` 和 `reactive` 区别**

`ref`

- 任意类型（基本类型/对象）
- `.value`获取或修改
- vue模板自动解包，无需`.value`
- `TypeScript` 需要泛型参数 `ref<string>`
- 解构后属性需通过`.value`
- 支持重新赋值整个对象
- 性能略低，有`.value`包装开销
- 通过`Object.defineProperty`劫持实现

 `reactive`-对象和数组

- 对象或者数组
- 直接属性访问
- 直接使用属性名
- `TypeScript`自动推断类型
- 解构失去响应性（需 `toRefs()` ）
- 性能略高，无额外包装开销
- `Proxy`代理

```vue
<script setup>
   function addTodo() {
        if (newTodo.value.trim()) {
    		todos.value.push({
      			id: Date.now(),
      			text: newTodo.value.trim(),
      			completed: false
    		})
    		newTodo.value = ''
  		}
    }
</script>
```

`setup()`  `script setup` 语法糖 在`day1`里有笔记

`trim()`：用于**删除字符串开头和结尾的空白字符**（包括空格、制表符、换行符等不可见字符）；核心作用是规范化用户输入或处理文本数据，避免因首尾空格导致逻辑错误。

`if`判断，`todos`数组增加一组对象；执行完之后，输入框为空

```vue
<script setup>
	const todos = ref([])
</script>
<template>
	<ul v-if="todos.length" class="todo-list">
      <li 
        v-for="todo in todos"
        :key="todo.id"
        :class="{ completed: todo.completed }"
        class="todo-item"
       >
        <input 
          type="checkbox"
          v-model="todo.completed"
          class="toggle"
         >
         <span>{{ todo.text }}</span>
         <button 
           @click="todos = todos.filter(t => t.id !== todo.id)"
           class="delete-btn"
          >×</button>
       </li>
     </ul>
</template>
```

已知`todos`数组每一个`todo`对象

```
{ 
  id: Date.now(),
  text: newTodo.value.trim(),
  completed: false
 }
```

```html
<ul v-if="todos.length">  <!-- 外层条件判断 -->
  <li v-for="todo in todos"  
      :key="todo.id"
      :class="{ completed: todo.completed }"
      class="todo-item"
   >  <!-- 内层循环 计算属性处理过滤逻辑-->
   </li>
</ul>

<!-- v-if 与 v-show -->

<!-- 当前代码使用的实际DOM添加/移除 -->
<ul v-if="todos.length">...</ul>

<!-- 如果改用CSS显示隐藏（DOM元素始终存在） -->
<ul v-show="todos.length">...</ul>
```

`:key` 最常用例与 `v-for` 结合；传了`key`，根据`key`的变化顺序来重新排列元素，始终移除/销毁 key 已经不存在的元素；同一个父元素下的子元素必须具有**唯一的 key**；重复的 key 将会导致渲染异常；也可用于强制替换一个元素/组件而不是复用它；

`:class` 绑定`class`属性，根据布尔值来判断；若存在多个类名，比如两个类名怎么写

```vue
<li :class="todo.completed === true ? completed : 类目名"></li>
```

```vue
<input 
   type="checkbox"
   v-model="todo.completed"
   class="toggle"
>
<span>{{ todo.text }}</span>
```

`v-model`双向绑定的数组中`completed`的布尔值

`{{ vue 模板语法 表示 todo.text 的值作为文本在span显示 }}`

```vue
<button 
  @click="todos = todos.filter(t => t.id !== todo.id)"
  class="delete-btn"
>
    ×
</button>
```

删除单个待办事项的**核心**逻辑

过滤逻辑：`todos.filter(t => t.id !== todo.id)`

基础知识

```js
const newArray = arr.fliter((element,index,originalArray) => {
	// 返回 true 保留元素，false 过滤元素
})
element 当前遍历的元素
index 当前元素下标，可选
originalArray 原始数组 arr 可选

不会改变原数组，始终返回新数组，原数组不变
所有元素被过滤，返回空数组
回调函数必须返回布尔值
arr空数组调用也不会报错
```

```vue
todos = ... 赋值操作触发vue的响应式更新，视图自动渲染，显示删除后列表
```

**推荐写法**

```vue
<script setup>
 const removeTodo = (todoId) => {
     todos.value = todos.value.filter(t => t.id !== todoId)
 }
</script>
<template>
	<button @click="removeTodo(todo.id)">
        x
    </button>
</template>
```

最后 `v-else` 和 `v-if`

```vue
<p v-else class="empty-tip">暂无待办事项，开始添加吧！</p>
```



```vue
<script setup>
// 持久化存储
onMounted(() => {
  const saved = localStorage.getItem('todos')
  if (saved) todos.value = JSON.parse(saved)
})

// 监听todos变化
watch(todos, (newVal) => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep: true })
</script>
```

`onMounted()`生命周期函数，用于在组件挂载到DOM后执行逻辑；

处理异步逻辑考虑错误捕获`(try/catch)`

`onBeforeMount`（挂载前）`onUpdated`（更新后）`onUnmounted`（卸载后）

```js
const saved = localStorage.getItem('todos')
if(saved) {
	todos.value = JSON.parse(saved)
}

获取localStorage存储的JSON字符串 getItem  setItem  removeItem
判断如果存在，解析数据 JSON.parse 并转成JS对象 最后更新响应式
```

```js
watch(todos, (newVal) => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep: true })
```

`JSON.stringify` 将对象转换成JSON字符串

`{ deep: true }` 默认跟踪整个数组替换，需要深度监听