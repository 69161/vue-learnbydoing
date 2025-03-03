<script setup>
import { ref, onMounted, watch } from 'vue'

// 响应式数据
const todos = ref([])
const newTodo = ref('')

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
.container { max-width: 600px; margin: 2rem auto; height: 100vh;}
.todo-input { flex: 1; padding: 0.8rem; }
.completed { text-decoration: line-through; opacity: 0.7; }
/* 更多样式... */
</style>