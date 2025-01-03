<template>
  <div class="app">
    <h1>
      <i class="fas fa-clipboard-list todo-icon"></i>
      待办事项清单
    </h1>
    <TodoList />
  </div>
</template>

<script setup>
import TodoList from './components/TodoList.vue';
</script>

<style>
.app {
  font-family: Arial, sans-serif;
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}

h1 {
  text-align: center;
  color: #333;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
}

.todo-icon {
  color: #FF6B6B;
  font-size: 0.9em;
  animation: bounce 2s infinite;
}

@keyframes bounce {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-5px); }
}
</style> 