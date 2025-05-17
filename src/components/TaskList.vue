<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

const tasks = ref([])
const editingTask = ref(null)
const editTitle = ref('')
const editDescription = ref('')

const startEditing = (task) => {
  editingTask.value = task.id
  editTitle.value = task.title
  editDescription.value = task.description
}

const updateTask = async (task) => {
  try {
    await axios.put(`http://localhost:8080/api/tasks/${task.id}`, {
      ...task,
      title: editTitle.value,
      description: editDescription.value
    })

    const taskIndex = tasks.value.findIndex(t => t.id === task.id)
    if (taskIndex !== -1) {
      tasks.value[taskIndex] = {
        ...task,
        title: editTitle.value,
        description: editDescription.value
      }
    }

    editingTask.value = null
  } catch (error) {
    console.error('Error updating task:', error)
  }
}

const cancelEditing = () => {
  editingTask.value = null
}

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

defineExpose({
  fetchTasks
})
</script>

<template>
  <div class="container">
    <div class="task-list">
      <div v-for="task in tasks" :key="task.id" class="task-card">
        <div class="task-content">
          <label class="ios-checkbox">
            <input 
              type="checkbox" 
              :checked="task.completed" 
              @change="toggleComplete(task)"
            >
            <span class="checkmark"></span>
          </label>
          <div v-if="editingTask === task.id" class="edit-form">
            <input 
              v-model="editTitle" 
              class="ios-input"
              placeholder="Task title"
            >
            <textarea 
              v-model="editDescription" 
              class="ios-input"
              placeholder="Task description"
            ></textarea>
            <div class="edit-buttons">
              <button @click="updateTask(task)" class="save-btn">Save</button>
              <button @click="cancelEditing" class="cancel-btn">Cancel</button>
            </div>
          </div>
          <div v-else :class="{ 'task-text': true, completed: task.completed }">
            <h3>{{ task.title }}</h3>
            <p>{{ task.description }}</p>
          </div>
        </div>
        <div class="action-buttons">
          <button v-if="!editingTask" @click="startEditing(task)" class="edit-btn">
            Edit
          </button>
          <button @click="deleteTask(task.id)" class="delete-btn">
            Delete
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
:root {
  --ios-background: #1C1C1E;
  --ios-container: #2C2C2E;
  --ios-text-primary: #FFFFFF;
  --ios-text-secondary: #A1A1AA;
  --ios-blue: #007AFF;
  --ios-red: #FF3B30;
  --ios-green: #30D158;
  --ios-grey: #636366;
}

body {
  background-color: var(--ios-background);
  color: var(--ios-text-primary);
  font-family: -apple-system, BlinkMacSystemFont, "San Francisco", "Helvetica Neue", sans-serif;
}

.container {
  width: 100%;
  max-width: 500px;
  margin: 1rem auto;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.task-list {
  flex: 1;
  overflow-y: auto;
  padding: 0.75rem;
  border-radius: 20px;
  background: var(--ios-container);
  backdrop-filter: blur(20px);
  border: 1px solid rgba(255, 255, 255, 0.1);
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.3);
  max-width: 500px;
  width: 100%;
}

.task-card {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 16px;
  margin: 0.75rem 0;
  background: var(--ios-container);
  border-radius: 16px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
  transition: all 0.2s ease-in-out;
  border: 1px solid rgba(255, 255, 255, 0.1);
  max-width: 500px;
  width: 100%;
}

.task-text {
  padding: 0 8px;
}

.task-content {
  display: flex;
  align-items: flex-start;
  gap: 1rem;
  flex: 1;
}

.task-text h3 {
  margin: 0;
  font-size: 1rem;
  font-weight: 500;
  color: var(--ios-text-primary);
}

.task-text p {
  margin: 0.25rem 0 0;
  font-size: 0.9rem;
  color: var(--ios-text-secondary);
}

.completed h3,
.completed p {
  text-decoration: line-through;
  color: rgba(235, 235, 245, 0.4);
}

.ios-checkbox {
  position: relative;
  display: inline-block;
  width: 22px;
  height: 22px;
}

.ios-checkbox input {
  opacity: 0;
  width: 0;
  height: 0;
}

.checkmark {
  position: absolute;
  top: 0;
  left: 0;
  width: 22px;
  height: 22px;
  background: #eee;
  border-radius: 50%;
  transition: all 0.2s ease;
}

.ios-checkbox input:checked ~ .checkmark {
  background: var(--ios-blue);
}

.checkmark:after {
  content: "";
  position: absolute;
  display: none;
  left: 8px;
  top: 4px;
  width: 5px;
  height: 10px;
  border: solid white;
  border-width: 0 2px 2px 0;
  transform: rotate(45deg);
}

.ios-checkbox input:checked ~ .checkmark:after {
  display: block;
}

.delete-btn, .edit-btn, .save-btn, .cancel-btn {
  color: white;
  border: none;
  padding: 12px 24px;
  border-radius: 9999px;
  font-size: 0.9rem;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.2s ease-in-out;
}

.delete-btn {
  background: var(--ios-red);
}

.edit-btn {
  background: var(--ios-blue);
}

.save-btn {
  background: var(--ios-green);
}

.cancel-btn {
  background: var(--ios-grey);
}

.delete-btn:hover, .edit-btn:hover, .save-btn:hover, .cancel-btn:hover {
  transform: scale(1.03);
}

.delete-btn:active, .edit-btn:active, .save-btn:active, .cancel-btn:active {
  transform: scale(0.97);
}

.action-buttons {
  display: flex;
  gap: 8px;
}

.edit-form {
  flex: 1;
  display: flex;
  flex-direction: column;
  gap: 8px;
  padding: 0 8px;
}

.ios-input {
  font-size: 16px;
  padding: 12px 16px;
  background: #3C3C3E;
  color: #FFFFFF;
  border-radius: 16px;
  border: none;
  font-family: -apple-system, BlinkMacSystemFont, "San Francisco", "Helvetica Neue", sans-serif;
  box-shadow: inset 0 2px 4px rgba(0, 0, 0, 0.1);
  transition: border-color 0.2s ease-in-out;
}

.ios-input:focus {
  outline: none;
  border-color: #0A84FF;
  box-shadow: 0 0 6px rgba(10, 132, 255, 0.5);
}

.edit-form textarea.ios-input {
  min-height: 60px;
}

.edit-buttons {
  display: flex;
  gap: 8px;
}

/* Custom scrollbar */
.task-list::-webkit-scrollbar {
  width: 8px;
}

.task-list::-webkit-scrollbar-track {
  background: transparent;
}

.task-list::-webkit-scrollbar-thumb {
  background: rgba(0, 0, 0, 0.1);
  border-radius: 4px;
}
</style>