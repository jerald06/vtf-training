<template>
  <button @click="toggleForm">Toggle Form</button>

  <p>
    {{ displayForm ? 'Form is shown' : 'Form is not shown' }}
  </p>

  <div v-show="displayForm">
    <form @submit.prevent="createTodo">
      <label for="todo-description">Description</label>
      <input v-model="data.todoDescription" type="text" id="todo-description">

      <input type="submit" value="Create">
    </form>

    <ol>
      <li v-for="(todo, index) in data.todos" :key="index">

        <!-- Edit mode -->
        <template v-if="editingIndex === index">
          <input v-model="editText" @keyup.enter="saveEdit(index)" @keyup.esc="cancelEdit" />
          <button @click="saveEdit(index)">Save</button>
          <button @click="cancelEdit">Cancel</button>
        </template>

        <!-- Normal mode -->
        <template v-else>
          <del v-if="todo.isCompleted">{{ todo.description }}</del>
          <span v-else>{{ todo.description }}</span>

          <button @click="startEdit(index)">Edit ✏️</button>
          <button @click="deleteTodo(index)">Delete 🗑️</button>
          <button v-if="!todo.isCompleted" @click="markAsComplete(index)">Complete ✅</button>
        </template>

      </li>
    </ol>
  </div>
</template>

<script setup>
import { reactive, ref } from "vue";

let displayForm = ref(false);

// for edit feature
let editingIndex = ref(null);
let editText = ref("");

const data = reactive({
  todos: [],
  todoDescription: ""
})

function createTodo() {
  data.todos.push({
    description: data.todoDescription,
    isCompleted: false
  });
  data.todoDescription = "";
}

function deleteTodo(index) {
  data.todos.splice(index, 1);
}

function markAsComplete(index) {
  data.todos[index].isCompleted = true;
}

function toggleForm() {
  displayForm.value = !displayForm.value;
}

function startEdit(index) {
  editingIndex.value = index;
  editText.value = data.todos[index].description;
}

function saveEdit(index) {
  if (editText.value.trim()) {
    data.todos[index].description = editText.value;
  }
  editingIndex.value = null;
}

function cancelEdit() {
  editingIndex.value = null;
}
</script>