
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
.container {
  width: 100%;
  max-width: 500px;
  margin: 1rem auto;
  padding: 0 1rem;
}

.task-list {
  max-height: 60vh;
  overflow-y: auto;
  padding: 0.5rem;
  border-radius: 16px;
  background: rgba(30, 30, 32, 0.8);
  backdrop-filter: blur(20px);
  border: 1px solid rgba(255, 255, 255, 0.1);
}

.task-card {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem;
  margin: 0.75rem 0;
  background: white;
  border-radius: 12px;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.05);
  transition: all 0.3s ease;
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
  color: #000;
}

.task-text p {
  margin: 0.25rem 0 0;
  font-size: 0.9rem;
  color: #666;
}

.completed h3,
.completed p {
  text-decoration: line-through;
  color: #999;
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
  background: #007AFF;
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

.delete-btn {
  background: #FF3B30;
  color: white;
  border: none;
  padding: 8px 12px;
  border-radius: 8px;
  font-size: 0.9rem;
  cursor: pointer;
  transition: all 0.3s ease;
}

.delete-btn:hover {
  background: #dc352b;
}

.edit-btn {
  background: #007AFF;
  color: white;
  border: none;
  padding: 8px 12px;
  border-radius: 8px;
  font-size: 0.9rem;
  cursor: pointer;
  transition: all 0.3s ease;
  margin-right: 8px;
}

.edit-btn:hover {
  background: #0066d6;
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
}

.edit-form .ios-input {
  font-size: 0.9rem;
  padding: 8px;
}

.edit-form textarea.ios-input {
  min-height: 60px;
}

.edit-buttons {
  display: flex;
  gap: 8px;
}

.save-btn {
  background: #34C759;
  color: white;
  border: none;
  padding: 6px 12px;
  border-radius: 8px;
  font-size: 0.9rem;
  cursor: pointer;
}

.cancel-btn {
  background: #8E8E93;
  color: white;
  border: none;
  padding: 6px 12px;
  border-radius: 8px;
  font-size: 0.9rem;
  cursor: pointer;
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
