<script setup>
/*
1. ref: how can handle state)
2. onMounted: use when we first render the page/component, get from local storage)
3. computed: to have things in order because we have array)
5. watch: watch for any changes for references)
*/
//Importing necessary functions from vue
import { ref, onMounted, computed, watch } from 'vue'

//Initialize reactive variables
const todos = ref([]) // Array to store todos
const name = ref('') // Name input field value

// Create refs for input fields
const input_content = ref('') // Content of todo
const input_category = ref(null) // Category of the todo

// Computed property to sort todos in descending order of creation time
const todos_asc = computed(()=> todos.value.sort((a,b) => {
  //return ascending list by data, latest by front and oldest at the back
  return b.createdAt - a.createdAt
}))

// Function to add a new todo
const addTodo = () => {
  //first condition: to check empty string = no content, if there is space it will remove
  //second condtion: to check if there is nothing there
  if (input_content.value.trim() === ''|| input_category.value === null){
    return
  }
  //Add new todo to the todos category
  todos.value.push({ //push content, category and time into todos
    content: input_content.value,
    category: input_category.value,
    done: false, // By default nothing gonna be set into done
    createdAt: new Date().getTime() // Setting creation time, Returns date in miliseconds which gonna use to compare inside in todos_asc
  })

  // Clearing input fields after adding todo
  input_content.value = ''
  input_category.value = null
}

// Funtion to remove a todo
const removeTodo = todo => {
  // Returns date in miliseconds which gonna use to compare inside in todos_asc
  // Loop through todo if not equal to todo then it will added back into the array if equal then it will not be added back
  todos.value = todos.value.filter(t => t !== todo)
}

// Watching for changes in todos array and updating local storage
watch(todos, newVal => {
  localStorage.setItem('todos', JSON.stringify(newVal))
},{deep: true})//deep gonna look into indv item in the array n capture and send it

// Watching for changes in name input field and updating local storage
watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

// Loading name and todos from local storage when the component is mounted
onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<!-- Template for the todo app -->
<template>
<main class="app">
  <!-- Greeting section with name input field -->
  <section class="greeting">
    <h2 class="title">
      What's up, <input type="text" placeholder="Name here" v-model="name" />
    </h2>
  </section>

  <!-- Section to create a new todo -->
  <section class="create-todo">
    <h3>CREATE A TODO</h3>

    <form @submit.prevent="addTodo">
      <h4>What's on your todo list?</h4>
      <input
        type="text"
        placeholder="e.g make a video"
        v-model="input_content" />

        <h4>Pick a category</h4>

        <div class="options">
          <!-- Radio button selecting business category -->
          <label>
            <input
              type="radio"
              name="category"
              value="business"
              v-model="input_category" />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>

          <!-- Radio button selecting personal category -->
          <label>
            <input
              type="radio"
              name="category"
              value="personal"
              v-model="input_category" />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        <!-- Submit button to add todo -->
        <input type="submit" value="Add todo">
    </form>
  </section>

  <!-- Section to display the todo list -->
  <section class="todo-list">
    <h3>TODO LIST</h3>
    <div class="list">
      <!-- Iterating over todos and displaying them -->
      <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
        <label>
          <!-- Checkbox for marking todo as done -->
          <input type="checkbox" v-model="todo.done" />
          <span :class="`bubble ${todo.category}`"></span>
        </label>
        <!-- Input field to edit todo content -->
        <div class="todo-content">
          <input type="text" v-model="todo.content">
        </div>
        <!-- Button to delete todo -->
        <div class="actions">
          <button class="delete" @click="removeTodo(todo)">Delete</button>
        </div>
      </div>
    </div>
  </section>
</main>

</template>


