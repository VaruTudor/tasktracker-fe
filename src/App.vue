
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
  padding: 2rem 1rem;
}

h1 {
  text-align: center;
  color: #000;
  font-weight: 600;
  font-size: 2rem;
  margin-bottom: 2rem;
}

body {
  background: #f2f2f7;
  min-height: 100vh;
}
</style>
