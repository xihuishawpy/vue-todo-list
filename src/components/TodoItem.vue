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
      class="todo-checkbox"
    >
    <span class="todo-text">{{ todo.text }}</span>
    <button @click="$emit('delete')" class="delete-btn">
      <i class="fas fa-trash-alt"></i>
    </button>
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
  padding: 15px;
  margin: 8px 0;
  background: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.05);
  transition: all 0.3s ease;
  animation: slideIn 0.3s ease;
  position: relative;
}

@keyframes slideIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.todo-item:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 8px rgba(0,0,0,0.1);
}

.completed {
  opacity: 0.7;
  background: #f8f9fa;
  text-decoration: line-through;
  color: #6c757d;
}

.todo-checkbox {
  margin-right: 12px;
  cursor: pointer;
}

.todo-text {
  flex: 1;
  margin-left: 8px;
  font-size: 16px;
  color: #2c3e50;
}

.delete-btn {
  margin-left: auto;
  border: none;
  background: none;
  color: #dc3545;
  cursor: pointer;
  opacity: 0.5;
  transition: all 0.2s ease;
  padding: 8px;
  border-radius: 4px;
  position: relative;
}

.delete-btn:hover {
  opacity: 1;
  background-color: rgba(220, 53, 69, 0.1);
}

.todo-item.dragging {
  opacity: 0.5;
  background: #f8f9fa;
}
</style> 