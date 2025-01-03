<template>
  <div 
    class="todo-item" 
    :class="{ completed: todo.completed }"
    draggable="true"
    @dragstart="$emit('dragstart', $event)"
    @dragend="$emit('dragend', $event)"
    @dragover.prevent
    @drop="$emit('drop', $event)"
  >
    <input 
      type="checkbox" 
      :checked="todo.completed"
      @change="$emit('toggle')"
    >
    <span>{{ todo.text }}</span>
    <button @click="$emit('delete')" class="delete-btn">×</button>
  </div>
</template>

<script setup>
defineProps({
  todo: {
    type: Object,
    required: true
  }
});

defineEmits(['toggle', 'delete', 'dragstart', 'dragend', 'drop']);
</script>

<style scoped>
.todo-item {
  display: flex;
  align-items: center;
  padding: 10px;
  margin: 5px 0;
  background: #fff;
  border-radius: 4px;
  box-shadow: 0 1px 3px rgba(0,0,0,0.1);
}

.completed {
  opacity: 0.6;
  text-decoration: line-through;
}

.delete-btn {
  margin-left: auto;
  border: none;
  background: none;
  color: #ff4444;
  cursor: pointer;
}
</style> 