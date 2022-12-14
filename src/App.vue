<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

// filtering todos
const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt
  })
)

// validate empty todo
const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    return
  }

  // push new todo content and category
  // ...and also track creation order
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime(),
  })

  // reset inputs after todo creation
  input_content.value = ''
  input_category.value = null
}

const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t != todo)
}

// listen to changes in todos state
watch(
  todos,
  (newVal) => {
    localStorage.setItem('todos', JSON.stringify(newVal))
  },
  { deep: true }
)

// watch for addition of new todos
watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

// watch for pop changes
onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos') || [])
})
</script>

<template>
  <main class="app">
    <h1>Todo List Built with VueJS</h1>

    <!-- todo section starts -->
    <section class="greeting">
      <h2 class="title">
        What's up, <input type="text" placeholder="Name here" v-model="name" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>CREATE A TODO</h3>

      <form @submit.prevent="addTodo">
        <h4>...it can be anything, literally!</h4>

        <input
          type="text"
          placeholder="e.g make a video"
          v-model="input_content"
        />

        <h4>Pick a category</h4>

        <div class="options">
          <!-- business label -->

          <label>
            <input
              type="radio"
              name="category"
              value="business"
              v-model="input_category"
            />

            <span class="bubble business"></span>

            <div>Business</div>
          </label>

          <!-- personal label -->

          <label>
            <input
              type="radio"
              name="category"
              value="personal"
              v-model="input_category"
            />

            <span class="bubble personal"></span>

            <div>Personal</div>
          </label>
        </div>

        <input type="submit" value="Add todo" />
      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <h4>Here you go Mister!</h4>

      <div
        v-for="todo in todos_asc"
        :class="`todo-item ${todo.done && 'done'}`"
      >
        <label>
          <input type="checkbox" v-model="todo.done" />

          <span :class="`bubble ${todo.category}`"></span>
        </label>

        <div class="todo-content">
          <input type="text" v-model="todo.content" />
        </div>

        <div class="actions">
          <button class="delete" @click="removeTodo(todo)">Delete</button>
        </div>
      </div>
    </section>

    <h4>This was created as a VueJS side project</h4>

    <span>
      &copy;
      <a href="https://www.youtube.com/c/TylerPotts">Tyler Potts</a></span
    >
  </main>
</template>
