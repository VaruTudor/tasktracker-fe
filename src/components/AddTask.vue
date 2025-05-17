
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
  background: rgba(255, 255, 255, 0.05);
  backdrop-filter: blur(10px);
  border-radius: 20px;
  border: 1px solid rgba(255, 255, 255, 0.1);
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.3);
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
  border-radius: 12px;
  background: #2C2C2E;
  color: #FFFFFF;
  font-size: 16px;
  transition: all 0.3s ease;
}

.ios-input::placeholder {
  color: rgba(235, 235, 245, 0.6);
}

.ios-input:focus {
  outline: none;
  background: #3C3C3E;
}

textarea.ios-input {
  min-height: 100px;
  resize: vertical;
}

.ios-button {
  background: #0A84FF;
  color: white;
  border: none;
  padding: 12px 20px;
  border-radius: 12px;
  font-size: 16px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s ease;
}

.ios-button:hover {
  background: #0071E3;
}

.ios-button:active {
  transform: scale(0.98);
}
</style>
