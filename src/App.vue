
<script setup>
import { ref } from 'vue'
import TaskList from './components/TaskList.vue'
import AddTask from './components/AddTask.vue'

const taskListRef = ref(null)

const refreshTasks = () => {
  taskListRef.value?.fetchTasks()
}
</script>

<template>
  <main>
    <h1>Tasks</h1>
    <AddTask @taskAdded="refreshTasks" />
    <TaskList ref="taskListRef" />
  </main>
</template>

<style>
main {
  max-width: 800px;
  margin: 0 auto;
  padding: 1rem;
  height: 100vh;
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

h1 {
  text-align: center;
  color: #fff;
  font-weight: 600;
  font-size: 1.5rem;
  margin: 0;
}
</style>
