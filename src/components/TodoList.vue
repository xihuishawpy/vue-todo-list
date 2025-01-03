<template>
  <div class="todo-list">
    <div class="todo-input">
      <input 
        v-model="newTodo" 
        @keyup.enter="addTodo"
        placeholder="添加新的待办事项..."
      >
      <button @click="addTodo">添加</button>
    </div>

    <div class="todos">
      <TodoItem
        v-for="(todo, index) in visibleTodos"
        :key="todo.id"
        :todo="todo"
        @toggle="toggleTodo(todo)"
        @delete="deleteTodo(todo)"
        @dragstart="handleDragStart($event, index)"
        @dragend="handleDragEnd"
        @drop="handleDrop($event, index)"
      />
    </div>

    <div v-if="collapsedCount > 0" class="collapse-info">
      <button @click="isCollapsed = !isCollapsed">
        {{ isCollapsed ? `展开剩余 ${collapsedCount} 项` : '折叠' }}
      </button>
    </div>

    <Celebrate v-if="allCompleted && todos.length > 0" />
    <Weather />
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';
import TodoItem from './TodoItem.vue';
import Celebrate from './Celebrate.vue';
import Weather from './Weather.vue';

const todos = ref([]);
const newTodo = ref('');
const isCollapsed = ref(true);
const draggedItem = ref(null);

const visibleTodos = computed(() => {
  return isCollapsed.value ? todos.value.slice(0, 3) : todos.value;
});

const collapsedCount = computed(() => {
  return Math.max(0, todos.value.length - 3);
});

const allCompleted = computed(() => {
  return todos.value.length > 0 && todos.value.every(todo => todo.completed);
});

function addTodo() {
  if (newTodo.value.trim()) {
    todos.value.push({
      id: Date.now(),
      text: newTodo.value,
      completed: false
    });
    newTodo.value = '';
  }
}

function deleteTodo(todo) {
  const index = todos.value.indexOf(todo);
  todos.value.splice(index, 1);
}

function toggleTodo(todo) {
  todo.completed = !todo.completed;
}

function handleDragStart(event, index) {
  draggedItem.value = index;
  event.target.style.opacity = '0.5';
}

function handleDragEnd(event) {
  event.target.style.opacity = '1';
}

function handleDrop(event, index) {
  const itemToMove = todos.value[draggedItem.value];
  todos.value.splice(draggedItem.value, 1);
  todos.value.splice(index, 0, itemToMove);
  draggedItem.value = null;
}
</script>

<style scoped>
.todo-list {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  padding-bottom: 160px;
}

.todo-input {
  display: flex;
  margin-bottom: 20px;
}

.todo-input input {
  flex: 1;
  padding: 8px;
  margin-right: 10px;
}

.collapse-info {
  text-align: center;
  margin-top: 10px;
}
</style> 