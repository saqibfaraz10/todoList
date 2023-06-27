<script setup>
import { computed, onMounted, ref, watch } from 'vue';

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)
const check = ref(false);

const todos_ascend = computed(() => todos.value.sort((a,b) => {
  return b.createdAt - a.createdAt
}))

const addTodo = () => {
  if(input_content.value.trim() === '' || input_category.value === null){
    return
  }
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime()
  })
  input_category.value = null
  input_content.value = ''
  }


const openDialog = () => {
  check.value = !check.value;
};

const closeDialog = () => {
  check.value = !check.value;
};

const removeTodo = (todo) =>{
  todos.value = todos.value.filter(t => t != todo)
}

watch(todos, newVal => {
  localStorage.setItem('todos', JSON.stringify(newVal))
})
watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
}, {deep: true})

onMounted(() =>{
  name.value = localStorage.getItem('name') || '';
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
  check.value = false
})
</script>

<template>
<main class="app">
<section class="greeting">
  <h2 class="title">
    What's up<input type="text" placeholder="Name here..." v-model="name"/>
  </h2>
</section>
<section class="create-todo">
  <h3>Create a new todo</h3>

  <form @submit.prevent="addTodo">
    <h4>What's on your todo list</h4>
    <input type="text" v-model="input_content" placeholder="e.g. making a video">
    <h4>Pick a category</h4>
    <div class="options">
      <label>
        <input 
          type="radio" 
          name="category" 
          value="business"  
          v-model="input_category" />
          <span class="bubble business"></span>
        <div>Business</div>
      </label>
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
    <input type="submit" value="Add Todo">
  </form>
</section>



<section class="todo-list">
  <!-- dialog box starts here-->
  <div id="dialog" class="dialog-box" v-show="check">
          <h2>Edit your list</h2>
          <button class="confirm-button" @click="closeDialog">Confirm</button>
        </div>
        <!-- dialog box ends here-->
  <h3>TODO LIST</h3>
  <div class="list">
    <div v-for="todo in todos_ascend" v-bind:key="todo" 
    :class="`todo-item ${todo.done && 
    'done'}`">
      

        <label>
          <input type="checkbox" checked v-model="todo.done"/>
          <span :class="`bubble ${todo.category}`"></span>
        </label>
        
        <div class="todo-content">
          <input type="text" v-model="todo.content" :readonly="!check"/>
        </div>

        <div class="actions">
          <button class="edit" @click="openDialog">
            Edit
          </button>
          <button class="delete" @click="removeTodo(todo)">
            Delete
          </button>
        </div>

    </div>

  </div>
</section>
</main>
</template>

<style scoped>

</style>
