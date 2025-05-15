
<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

const tasks = ref([])

const fetchTasks = async () => {
  try {
    const response = await axios.get('http://0.0.0.0:8080/api/tasks')
    tasks.value = response.data
  } catch (error) {
    console.error('Error fetching tasks:', error)
  }
}

const deleteTask = async (id) => {
  try {
    await axios.delete(`http://0.0.0.0:8080/api/tasks/${id}`)
    tasks.value = tasks.value.filter(task => task.id !== id)
  } catch (error) {
    console.error('Error deleting task:', error)
  }
}

const toggleComplete = async (task) => {
  try {
    await axios.put(`http://0.0.0.0:8080/api/tasks/${task.id}`, {
      ...task,
      completed: !task.completed
    })
    task.completed = !task.completed
  } catch (error) {
    console.error('Error updating task:', error)
  }
}

onMounted(fetchTasks)
</script>

<template>
  <div class="task-list">
    <h2>Tasks</h2>
    <div v-for="task in tasks" :key="task.id" class="task-item">
      <div class="task-content">
        <input type="checkbox" :checked="task.completed" @change="toggleComplete(task)">
        <div :class="{ completed: task.completed }">
          <h3>{{ task.title }}</h3>
          <p>{{ task.description }}</p>
        </div>
      </div>
      <button @click="deleteTask(task.id)" class="delete-btn">Delete</button>
    </div>
  </div>
</template>

<style scoped>
.task-list {
  max-width: 600px;
  margin: 0 auto;
}

.task-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem;
  margin: 0.5rem 0;
  border: 1px solid #ddd;
  border-radius: 4px;
}

.task-content {
  display: flex;
  align-items: center;
  gap: 1rem;
}

.completed {
  text-decoration: line-through;
  color: #888;
}

.delete-btn {
  background-color: #ff4444;
  color: white;
  border: none;
  padding: 0.5rem 1rem;
  border-radius: 4px;
  cursor: pointer;
}
</style>
