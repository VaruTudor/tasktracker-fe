<script setup>
import TaskList from './components/TaskList.vue'
import AddTask from './components/AddTask.vue'
</script>

<template>
  <main>
    <h1>Task Manager</h1>
    <AddTask @taskAdded="$refs.taskList.fetchTasks()" />
    <TaskList ref="taskList" />
  </main>
</template>

<style>
main {
  padding: 2rem;
}

h1 {
  text-align: center;
  color: #2c3e50;
  margin-bottom: 2rem;
}
</style>