
<script setup>
import { ref } from 'vue'
import axios from 'axios'

const title = ref('')
const description = ref('')

const emit = defineEmits(['taskAdded'])

const addTask = async () => {
  if (!title.value.trim()) return
  
  try {
    await axios.post('http://0.0.0.0:8080/api/tasks', {
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
    <h2>Add New Task</h2>
    <form @submit.prevent="addTask">
      <input v-model="title" placeholder="Task title" required>
      <textarea v-model="description" placeholder="Task description"></textarea>
      <button type="submit">Add Task</button>
    </form>
  </div>
</template>

<style scoped>
.add-task {
  max-width: 600px;
  margin: 2rem auto;
}

form {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

input, textarea {
  padding: 0.5rem;
  border: 1px solid #ddd;
  border-radius: 4px;
}

button {
  background-color: #4CAF50;
  color: white;
  border: none;
  padding: 0.5rem;
  border-radius: 4px;
  cursor: pointer;
}
</style>
