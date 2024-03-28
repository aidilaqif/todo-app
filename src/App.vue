<script setup>
/*
ref(how can handle state)
onMounted(use when we first render the page/component, get from local storage)
computed(to have things in order because we have array)
watch(watch for any changes for references)
*/
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

//input = content
const input_content = ref('')
//gonna be null by default
const input_category = ref(null)
//asc = ascending
const todos_asc = computed(()=> todos.value.sort((a,b) => {
  //return ascending list by data, latest by front and oldest at the back
  return b.createdAt - a.createdAt
}))

const addTodo = () => {
  //first condition: to check empty string = no content, if there is space it will remove
  //second condtion: to check if there is nothing there
  if (input_content.value.trim() === ''|| input_category.value === null){
    return
  }
  //push content, category and time into todos
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    //by default nothing gonna be set into done
    done: false,
    //returns date in miliseconds which gonna use to compare inside in todos_asc
    createdAt: new Date().getTime()
  })
  //to remove content when submit
  input_content.value = ''
  input_category.value = null
}

const removeTodo = todo => {
  //loop through todo if not equal to todo then it will added back into the array if equal then it will not be added back
  todos.value = todos.value.filter(t => t !== todo)
}

watch(todos, newVal => {
  localStorage.setItem('todos', JSON.stringify(newVal))
},{deep: true})//deep gonna look into indv item in the array n capture and send it

watch(name, (newVal) => {
  //watching name, if name changes it will set it to new value, and update local storage
  localStorage.setItem('name', newVal)
})
//to save name in local storage
onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>

<main class="app">
  <section class="greeting">
    <h2 class="title">
      What's up, <input type="text" placeholder="Name here" v-model="name" />
    </h2>
  </section>

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

          <!-- radio selection for business category -->
          <label>
            <input
              type="radio"
              name="category"
              value="business"
              v-model="input_category" />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>

          <!-- radio selection for personal category -->
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
        <input type="submit" value="Add todo">
    </form>
  </section>

  <section class="todo-list">
    <!-- todo list selection -->
    <h3>TODO LIST</h3>
    <div class="list">
      <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">

        <!-- checkbox -->
        <label>
          <input type="checkbox" v-model="todo.done" />
          <span :class="`bubble ${todo.category}`"></span>
        </label>

        <!-- content of checkbox -->
        <div class="todo-content">
          <input type="text" v-model="todo.content">
        </div>

        <div class="actions">
          <button class="delete" @click="removeTodo(todo)">Delete</button>
        </div>
      </div>
    </div>
  </section>
</main>

</template>


