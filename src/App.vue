<script setup>
import { ref, onMounted, computed, watch } from "vue";
const todos = ref([]);
const name = ref("");
const input_content = ref("");
const input_category = ref(null);
const todos_sorted = computed(() =>
  todos.value.sort((a, b) => {
    return b._createdAt - a._createdAt;
  })
);

watch(name, (newName) => {
  localStorage.setItem("name", newName);
});
onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});
const addTodo = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }
  todos.value.push({
    content: input_content.value,
    catagory: input_category.value,
    done: false,
    _createdAt: new Date().getTime(),
  });
  input_category.value = null;
  input_content.value = "";
};
const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};

watch(
  todos,
  (newTodos) => {
    localStorage.setItem("todos", JSON.stringify(newTodos));
  },
  { deep: true }
);
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        what's up, <input type="text" placeholder="Name Here" v-model="name" />
      </h2>
    </section>
    <section class="create-todo">
      <h3>CREATE TODOS</h3>
      <form @submit.prevent="addTodo">
        <h4>Whats on your todo lists?</h4>
        <input placeholder="e.g. make a video" type="text" name="todo" id="todo" v-model="input_content" />

        <h4>Pick a Catagory!</h4>
        <div class="options">
          <label for="catagory1">
            <input type="radio" name="catagory1" id="catagory1" value="business" v-model="input_category" />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>

          <label for="catagory2">
            <input type="radio" name="catagory2" id="catagory2" value="personal" v-model="input_category" />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        <input type="submit" value="Add Todo" />
      </form>
    </section>
    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list">
        <div v-for="todo in todos_sorted" :class="`todo-item ${todo.done && 'done'}`">
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.catagory}`"></span>
          </label>
          <div class="todo-content">
            <input type="text" v-model="todo.content" />
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
