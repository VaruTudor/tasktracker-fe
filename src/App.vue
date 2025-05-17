
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
  max-width: 500px;
  margin: 0 auto;
  padding: 1rem;
  height: 100vh;
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

h1 {
  text-align: center;
  color: #fff;
  font-weight: 600;
  font-size: 1.75rem;
  margin: 0;
  font-family: -apple-system, BlinkMacSystemFont, "San Francisco", "Helvetica Neue", sans-serif;
}
</style>
