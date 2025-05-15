<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

const tasks = ref([])

const fetchTasks = async () => {
  try {
    const response = await axios.get('http://localhost:8080/api/tasks')
    tasks.value = response.data
  } catch (error) {
    console.error('Error fetching tasks:', error)
  }
}

const deleteTask = async (id) => {
  try {
    await axios.delete(`http://localhost:8080/api/tasks/${id}`)
    tasks.value = tasks.value.filter(task => task.id !== id)
  } catch (error) {
    console.error('Error deleting task:', error)
  }
}

const toggleComplete = async (task) => {
  try {
    await axios.put(`http://localhost:8080/api/tasks/${task.id}`, {
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
  <div class="container">
    <h2>📋 Task List</h2>
    <div v-for="task in tasks" :key="task.id" class="task-card">
      <div class="task-content">
        <input type="checkbox" :checked="task.completed" @change="toggleComplete(task)" />
        <div :class="{ completed: task.completed }">
          <h3>{{ task.title }}</h3>
          <p>{{ task.description }}</p>
        </div>
      </div>
      <button @click="deleteTask(task.id)" class="delete-btn">✕</button>
    </div>
  </div>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600&display=swap');

* {
  box-sizing: border-box;
}

body {
  font-family: 'Inter', sans-serif;
}

.container {
  max-width: 600px;
  margin: 2rem auto;
  padding: 1rem;
  background: linear-gradient(145deg, #f0f0f0, #e0e0e0);
  border-radius: 20px;
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.05);
}

h2 {
  text-align: center;
  margin-bottom: 1rem;
  font-weight: 600;
  color: #333;
}

.task-card {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background: linear-gradient(145deg, #ffffff, #f5f5f5);
  padding: 1rem;
  margin: 0.75rem 0;
  border-radius: 16px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.05);
  transition: all 0.3s ease;
}

.task-card:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 12px rgba(0, 0, 0, 0.1);
}

.task-content {
  display: flex;
  align-items: center;
  gap: 1rem;
  flex: 1;
}

.task-content input[type="checkbox"] {
  width: 20px;
  height: 20px;
  accent-color: #007aff; /* iOS blue */
}

.task-content h3 {
  margin: 0;
  font-size: 1rem;
  font-weight: 600;
  color: #222;
}

.task-content p {
  margin: 0;
  font-size: 0.9rem;
  color: #555;
}

.completed h3,
.completed p {
  text-decoration: line-through;
  color: #999;
}

.delete-btn {
  background: #ff3b30;
  color: white;
  border: none;
  font-size: 1.2rem;
  padding: 0.4rem 0.8rem;
  border-radius: 10px;
  cursor: pointer;
  transition: background 0.3s;
}

.delete-btn:hover {
  background: #e02922;
}
</style>
