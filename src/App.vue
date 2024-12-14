<script setup>
import TaskList from './components/TaskList.vue'
</script>

<template>
  <div class="min-h-screen bg-gray-50 rounded-md py-8 font-roboto">
    <div class="container mx-auto px-4">
      <h1 class="text-3xl font-bold text-center mb-8 text-gray-800">Task Manager</h1>
      <TaskList />
    </div>
  </div>
</template>

<style>
@import url('https://fonts.googleapis.com/css2?family=Roboto:wght@300;400;500;700&display=swap');
</style>
