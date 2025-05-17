
<script setup>
import { ref } from 'vue'
import axios from 'axios'

const title = ref('')
const description = ref('')

const emit = defineEmits(['taskAdded'])

const addTask = async () => {
  if (!title.value.trim()) return
  
  try {
    const response = await axios.post('http://localhost:8080/api/tasks', {
      title: title.value,
      description: description.value,
      completed: false
    })
    title.value = ''
    description.value = ''
    emit('taskAdded')
  } catch (error) {
    console.error('Error adding task:', error)
  }
}
</script>

<template>
  <div class="add-task">
    <form @submit.prevent="addTask">
      <input 
        v-model="title" 
        placeholder="Task title" 
        required
        class="ios-input"
      >
      <textarea 
        v-model="description" 
        placeholder="Task description"
        class="ios-input"
      ></textarea>
      <button type="submit" class="ios-button">Add Task</button>
    </form>
  </div>
</template>

<style scoped>
.add-task {
  width: 100%;
  max-width: 500px;
  margin: 0 auto;
  padding: 1.5rem;
  background: rgba(30, 30, 32, 0.8);
  backdrop-filter: blur(20px);
  border-radius: 16px;
  border: 1px solid rgba(255, 255, 255, 0.1);
}

form {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.ios-input {
  width: 100%;
  padding: 12px 16px;
  border: none;
  border-radius: 10px;
  background: rgba(142, 142, 147, 0.12);
  font-size: 16px;
  transition: all 0.3s ease;
}

.ios-input:focus {
  outline: none;
  background: rgba(142, 142, 147, 0.18);
}

textarea.ios-input {
  min-height: 100px;
  resize: vertical;
}

.ios-button {
  background: #007AFF;
  color: white;
  border: none;
  padding: 12px;
  border-radius: 10px;
  font-size: 16px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s ease;
}

.ios-button:hover {
  background: #0066d6;
}

.ios-button:active {
  transform: scale(0.98);
}
</style>
