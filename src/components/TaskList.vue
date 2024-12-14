<script setup>
import { ref, onMounted, computed } from 'vue';

// Global reactive state for tasks
const tasks = ref([]);
const showForm = ref(false);
const newTaskName = ref('');
const showCompletedOnly = ref(false);

// Load tasks from localStorage on component mount
onMounted(() => {
  const savedTasks = localStorage.getItem('tasks');
  if (savedTasks) {
    tasks.value = JSON.parse(savedTasks);
  }
});

// Save tasks to localStorage whenever they change
const saveTasks = () => {
  localStorage.setItem('tasks', JSON.stringify(tasks.value));
};

// Computed property for filtered tasks
const filteredTasks = computed(() => {
  return showCompletedOnly.value
    ? tasks.value.filter(task => task.completed)
    : tasks.value;
});

// Task validation function
const isValidTask = (taskName) => {
  return taskName.length >= 3;
};

// Add new task
const addTask = () => {
  if (isValidTask(newTaskName.value)) {
    tasks.value.push({
      id: Date.now(),
      name: newTaskName.value,
      completed: false
    });
    newTaskName.value = '';
    saveTasks();
  }
};

// Toggle task completion
const toggleTask = (task) => {
  task.completed = !task.completed;
  saveTasks();
};

// Delete task
const deleteTask = (taskId) => {
  tasks.value = tasks.value.filter(task => task.id !== taskId);
  saveTasks();
};

// Toggle form visibility
const toggleForm = () => {
  showForm.value = !showForm.value;
};
</script>

<template>
  <div class="max-w-2xl mx-auto p-6">
    <div class="mb-6">
      <button 
        @click="toggleForm"
        class="bg-blue-500 hover:bg-blue-600 text-white font-bold py-2 px-4 rounded"
      >
        {{ showForm ? 'Hide Form' : 'Add New Task' }}
      </button>
      <button 
        @click="showCompletedOnly = !showCompletedOnly"
        class="ml-4 bg-purple-500 hover:bg-purple-600 text-white font-bold py-2 px-4 rounded"
      >
        {{ showCompletedOnly ? 'Show All Tasks' : 'Show Completed Only' }}
      </button>
    </div>

    <!-- Add Task Form -->
    <div v-if="showForm" class="mb-6 p-4 bg-gray-100 rounded">
      <input 
        v-model="newTaskName"
        type="text"
        placeholder="Enter task name (min 3 characters)"
        class="w-full p-2 mb-2 border rounded"
        @keyup.enter="addTask"
      />
      <button 
        @click="addTask"
        class="bg-green-500 hover:bg-green-600 text-white font-bold py-2 px-4 rounded"
        :disabled="!isValidTask(newTaskName)"
      >
        Add Task
      </button>
    </div>

    <!-- Task List -->
    <div v-if="tasks.length === 0" class="text-center text-gray-600">
      No tasks available
    </div>
    <div v-else class="space-y-2">
      <div 
        v-for="task in filteredTasks" 
        :key="task.id"
        class="flex items-center justify-between p-3 rounded transition-all"
        :class="{
          'bg-green-100': task.completed,
          'border-red-500 border-2': task.completed,
          'bg-white border': !task.completed
        }"
      >
        <div class="flex items-center">
          <input 
            type="checkbox"
            :checked="task.completed"
            @change="toggleTask(task)"
            class="mr-3 h-5 w-5"
          />
          <span class="text-gray-800" :class="{ 'line-through': task.completed }">{{ task.name }}</span>
        </div>
        <button 
          @click="deleteTask(task.id)"
          class="bg-red-500 hover:bg-red-600 text-white font-bold py-1 px-3 rounded"
        >
          Delete
        </button>
      </div>
    </div>
  </div>
</template>
