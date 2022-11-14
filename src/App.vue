<script setup>
import { ref, onMounted, computed, watch } from "vue";

const todos = ref([]);
const name = ref("");

const input_content = ref("");
const input_category = ref(null);
const input_priority = ref(null);

const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    if (b.priortity.length === a.priortity.length) {
      return b.createdAt - a.createdAt;
    }
    return b.priortity.length - a.priortity.length;
  })
);

const addTodo = () => {
  if (input_content.value.trim() === "") {
    window.alert("Please input something to add to your list");
    return;
  }

  if (input_category.value === null) {
    window.alert("Please select a category");
    return;
  }

  if (input_priority.value === null) {
    window.alert("Please select a priority");
    return;
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    priortity: input_priority.value,
    done: false,
    createdAt: new Date().getTime(),
  });

  input_category.value = null;
  input_priority.value = null;
  input_content.value = "";
};

const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};

watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  { deep: true }
);

watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Welcome,

        <input type="text" placeholder="Input name here" v-model="name" />
      </h2>
    </section>
    <div class="dif-sections">
      <section class="create-todo">
        <h3>Create a Todo List</h3>
        <form @submit.prevent="addTodo">
          <h4>What is on your todo list?</h4>
          <input
            type="text"
            placeholder="e.g walk the dog"
            v-model="input_content"
          />

          <h4>Pick a priority</h4>

          <div class="options">
            <label>
              <input
                type="radio"
                name="priority"
                value="urgent"
                v-model="input_priority"
              />
              <span class="bubble urgent"></span>
              <div>Urgent</div>
            </label>

            <label>
              <input
                type="radio"
                name="priority"
                value="major"
                v-model="input_priority"
              />
              <span class="bubble major"></span>
              <div>Major</div>
            </label>

            <label>
              <input
                type="radio"
                name="priority"
                value="mild"
                v-model="input_priority"
              />
              <span class="bubble mild"></span>
              <div>Mild</div>
            </label>

            <label>
              <input
                type="radio"
                name="priority"
                value="low"
                v-model="input_priority"
              />
              <span class="bubble low"></span>
              <div>Low</div>
            </label>
          </div>

          <h4>Pick a category</h4>

          <div class="options">
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
        <h3>Todo List</h3>

        <div class="list">
          <div
            v-for="todo in todos_asc"
            :class="`todo-item ${todo.done && 'done'}`"
          >
            <label>
              <input type="checkbox" v-model="todo.done" />
              <span :class="`bubble ${todo.priortity}`"></span>
            </label>

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
        </div>
      </section>
    </div>
  </main>
</template>
