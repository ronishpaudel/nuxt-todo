// pages/index.vue
<template>
  <div class="min-h-screen bg-gray-100 py-8 flex items-center justify-center">
    <div class="w-full max-w-md mx-auto bg-white rounded-lg shadow-md overflow-hidden">
      <div class="px-6 py-4 bg-indigo-600 w-full">
        <h1 class="text-2xl font-bold text-white">My Todo List</h1>
      </div>
      
      <div class="p-6 w-full">
        <!-- Add Todo Form -->
        <div class="flex mb-6">
          <input
            v-model="newTodo"
            type="text"
            placeholder="Add a new task..."
            class="flex-grow px-4 py-2 border rounded-l focus:outline-none focus:ring-2 focus:ring-indigo-500"
            @keyup.enter="addTodo"
          />
          <button
            @click="addTodo"
            class="bg-indigo-600 text-white px-4 py-2 rounded-r hover:bg-indigo-700 focus:outline-none"
          >
            Add
          </button>
        </div>
        
        <!-- Filter Controls -->
        <div class="flex justify-between mb-4">
          <div class="space-x-2">
            <button 
              @click="filter = 'all'" 
              class="px-3 py-1 text-sm rounded"
              :class="filter === 'all' ? 'bg-indigo-600 text-white' : 'bg-gray-200'"
            >
              All
            </button>
            <button 
              @click="filter = 'active'" 
              class="px-3 py-1 text-sm rounded"
              :class="filter === 'active' ? 'bg-indigo-600 text-white' : 'bg-gray-200'"
            >
              Active
            </button>
            <button 
              @click="filter = 'completed'" 
              class="px-3 py-1 text-sm rounded"
              :class="filter === 'completed' ? 'bg-indigo-600 text-white' : 'bg-gray-200'"
            >
              Completed
            </button>
          </div>
          <button 
            v-if="completedCount > 0"
            @click="clearCompleted" 
            class="text-sm text-gray-500 hover:text-gray-700"
          >
            Clear completed
          </button>
        </div>
        
        <!-- Todo List -->
        <div v-if="filteredTodos.length > 0" class="space-y-2">
          <div 
            v-for="todo in filteredTodos" 
            :key="todo.id"
            class="flex items-center p-3 border-b group hover:bg-gray-50"
          >
            <input
              type="checkbox"
              :checked="todo.completed"
              @change="toggleTodo(todo.id)"
              class="h-5 w-5 text-indigo-600 focus:ring-indigo-500 rounded"
            />
            <span 
              class="flex-grow px-3"
              :class="{'line-through text-gray-400': todo.completed}"
            >
              {{ todo.text }}
            </span>
            <button
              @click="removeTodo(todo.id)"
              class="text-red-500 hover:text-red-700 focus:outline-none opacity-0 group-hover:opacity-100 transition-opacity"
            >
              Delete
            </button>
          </div>
        </div>
        
        <!-- Empty State -->
        <div v-else class="text-center py-8 text-gray-500">
          <p v-if="filter === 'all'">Your todo list is empty. Add a new task above!</p>
          <p v-else-if="filter === 'active'">No active tasks found.</p>
          <p v-else>No completed tasks found.</p>
        </div>
        
        <!-- Todo Count -->
        <div class="mt-4 text-sm text-gray-500">
          {{ activeCount }} item{{ activeCount !== 1 ? 's' : '' }} left
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';

// State
const todos = ref([
  { id: 1, text: 'Learn Nuxt 3', completed: false },
  { id: 2, text: 'Build a Todo App', completed: false },
  { id: 3, text: 'Deploy to production', completed: false },
]);
const newTodo = ref('');
const filter = ref('all');

// Get next ID
const getNextId = () => {
  return todos.value.length ? Math.max(...todos.value.map(t => t.id)) + 1 : 1;
};

// Computed properties
const filteredTodos = computed(() => {
  if (filter.value === 'active') {
    return todos.value.filter(todo => !todo.completed);
  } else if (filter.value === 'completed') {
    return todos.value.filter(todo => todo.completed);
  }
  return todos.value;
});

const activeCount = computed(() => {
  return todos.value.filter(todo => !todo.completed).length;
});

const completedCount = computed(() => {
  return todos.value.filter(todo => todo.completed).length;
});

// Methods
const addTodo = () => {
  if (newTodo.value.trim()) {
    todos.value.push({
      id: getNextId(),
      text: newTodo.value.trim(),
      completed: false
    });
    newTodo.value = '';
  }
};

const toggleTodo = (id) => {
  const todo = todos.value.find(todo => todo.id === id);
  if (todo) {
    todo.completed = !todo.completed;
  }
};

const removeTodo = (id) => {
  todos.value = todos.value.filter(todo => todo.id !== id);
};


</script>