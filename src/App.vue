<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([]);
const name = ref('');

const input_content = ref('');
const input_category = ref(null);

const error_message = ref('');

const todos_asc = computed(() => todos.value.sort((a, b) => b.createdAt - a.createdAt));

const addTodos = () => {
  if (input_content.value === '' || input_category.value === null) return error_message.value = 'Please fill all the fields';
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    isDone: false,
    createdAt: new Date().getTime()
  });
  input_content.value = '';
  input_category.value = null;
  error_message.value = '';
};

const removeTodo = (todo) => {
  const index = todos.value.indexOf(todo);
  todos.value.splice(index, 1);
};

watch(todos, (newVal) => {
  localStorage.setItem('todos', JSON.stringify(newVal));
  console.log(localStorage.getItem('todos'));
}, { deep: true });

watch(name, (newVal) => {
  localStorage.setItem('name', newVal);
  console.log(localStorage.getItem('name'));
});

onMounted(() => {
  name.value = localStorage.getItem('name') || ' ';
  todos.value = JSON.parse(localStorage.getItem('todos')) || [];
});

</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2> What's Up,
        <input type="text" placeholder="Name here" v-model="name">
      </h2>
    </section>
    <section class="create-todo">
      <h3>CRATE A TODO </h3>
      <form @submit.prevent="addTodos">
        <h4>What is your todo ?</h4>
        <input type="text" placeholder="Todo content" v-model="input_content">
        <h4>What is your category ?</h4>
        <div class="options">
          <label>
            <input type="radio" name="category" value="business" v-model="input_category">
            <span class="bubble business"></span>
            <div class="text">Business</div>
          </label>
          <label>
            <input type="radio" name="category" value="personal" v-model="input_category">
            <span class="bubble personal"></span>
            <div class="text">Personal</div>
          </label>
        </div>
        <input type="submit" value="Add Todo">
      </form>
    </section>
    <section class="error">
      <p>{{ error_message  }}</p>
    </section>
    <section class="todo-list">
      <h2>Todo List</h2>
      <div class="list">
        <div class="todo-item" v-for="(todo,index) in todos_asc" :key="index"
          :class="` todo-item ${todo.isDone && isDone}`">
          <label>
            <input type="checkbox" v-model="todo.isDone">
            <span :class="`bubble ${todo.category}`"></span>
          </label>
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

<style scoped></style>
