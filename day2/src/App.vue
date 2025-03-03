<script setup>
import { ref, onMounted, watch, computed } from 'vue'

// 响应式数据
const todos = ref([])
const newTodo = ref('')

// filterText 
const filterText = {
  all: '全部',
  completed: '已完成',
  active: '未完成'
}
// 当前过滤条件
const currentFilter = ref('all')
// 过滤后的待办事项
const filteredTodos = computed(() => {
  switch (currentFilter.value) {
    case 'active':
      return todos.value.filter(todo =>!todo.completed)
    case 'completed':
      return todos.value.filter(todo => todo.completed)
    default:
      return todos.value
  }
  // if(currentFilter.value ==='all') {
  //   return todos.value
  // } else if(currentFilter.value === 'active') {
  //   return todos.value.filter(todo => !todo.completed)
  // } else {
  //   return todos.value.filter(todo => todo.completed)
  // }
  
})
  
// 添加待办事项
const addTodo = () => {
  if (newTodo.value.trim()) {
    todos.value.unshift({
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
    <h2>智能待办清单</h2>
    <div class="input-group">
      <input 
        v-model="newTodo"
        @keyup.enter="addTodo"
        placeholder="输入待办事项..."
        class="todo-input"
      >
      <button @click="addTodo" class="add-btn">添加</button>
    </div>
    <!-- 分类过滤 -->
    <div class="filter">
      <!-- v-for 遍历数组 -->
      <span
        v-for="filter in ['all', 'completed', 'active']"
        :key="filter"
        class="filter-item"
        :class="{ active: currentFilter === filter }"
        @click="currentFilter = filter"
      >{{ filterText[filter] }}</span>
    </div>
    <!-- 遍历todos 改为filteredTodos -->
    <ul v-if="filteredTodos.length" class="todo-list">
      <li 
        v-for="todo in filteredTodos"
        :key="todo.id"        
        class="todo-item"
      >
        <div class="todo-item-content"
          :class="{ completed: todo.completed }"
        >
          <input 
            type="checkbox"
            v-model="todo.completed"
            class="toggle"
          >
          <span class="text">{{ todo.text }}</span>
        </div>
        
        <div
          @click="todos = todos.filter(t => t.id !== todo.id)"
          class="delete-btn"
        >D</div>
      </li>
    </ul>
    
    <p v-else class="empty-tip">暂无待办事项，开始添加吧！</p>
  </div>
</template>

<style scoped>
/* 分类过滤 */
.filter {
  display: flex;
  margin-top: 1rem;
  margin-bottom: 1rem;
  justify-content: space-between;
  align-items: center;
  gap: 1rem;

}
/* 分类过滤项 */
.filter-item {
  flex: auto;
  border: 1px solid #ccc;
  cursor: pointer;
  border-radius: 5px;
  padding: 0.5rem 0.5rem;
  user-select: none;
}
/* 分类过滤项激活样式 */
.filter-item.active {
  background-color: #4CAF50;
  color: #fff;
}

/* 添加你的CSS样式 */
.container { 
  max-width: 600px; 
  margin: 2rem auto; 
  height: 100vh;
  border: 1px solid #ccc;
  border-radius: 5px;
  padding: 2rem;
  box-sizing: border-box;
}
.input-group { 
  display: flex; 
  align-items: center; 
  margin-bottom: 1rem; 
}
.todo-input { 
  flex: 1; 
  padding: 0.8rem; 
  outline: none; 
  border: none; 
  border-radius: 5px 0 0 5px; 
  height: 50px;
  box-sizing: border-box;
}
.add-btn {  
  padding: 0.8rem; 
  background-color: #4CAF50; 
  color: #fff; 
  border: none; 
  border-radius: 0 5px 5px 0; 
  cursor: pointer; 
  outline: none;
  height: 50px;
  box-sizing: border-box;
}
/* 超出范围隐藏滚动条 */
.todo-list {
  list-style: none; 
  padding: 0; 
  margin: 0; 
  max-height: 200px;
  overflow-y: auto;
}
.todo-list::-webkit-scrollbar {
  display: none;
}
.todo-item { 
  display: flex; 
  align-items:flex-start; 
  justify-content: space-between;
  padding: 1rem;
  border-radius: 5px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}
.todo-item:hover {
  background-color: #f5f5f5;
  color: black;
}
.todo-item-content {
  flex: 1;
  display: flex; 
  align-items: baseline; 
}
.toggle {
  margin-right: 1rem; 
  cursor: pointer;
}
.todo-item-content .text {
  display: inline-block;
  flex-grow: 1;
  margin-right: 1rem;
  word-wrap: break-word;
  white-space: pre-wrap;
  word-break: break-all;
  text-align: left;
}

.delete-btn {
  width: 28px;  /* 固定宽高 */
  height: 28px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 0; /* 清除默认padding */
  border: 1px solid #ccc;
  cursor: pointer;
}
.delete-btn:hover {
  background-color:brown;
  color: white;
}
.completed { 
  text-decoration: line-through; 
  opacity: 0.7; 
}
/* 更多样式... */
</style>