<template>
  <button @click="toggleForm">Toggle Form</button>

  <p>
    {{ displayForm ? 'Form is shown' : 'Form is not shown' }}
  </p>

  <div v-show="displayForm">
    <form @submit.prevent="createTodo">
      <label for="todo-description">Description</label>
      <input v-model="data.todoDescription" type="text" id="todo-description">

       <label for="todo-color">Color</label>
      <input v-model="data.todoColor" type="color" id="todo-color">

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

        <template v-else>
          <span
            :style="{
              display: 'inline-block',
              width: '20px',
              height: '12px',
              marginRight: '8px',
              backgroundColor: todo.color
            }"
          ></span>
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
  todoDescription: "",
  todoColor: "#4f46e5"
 
})

function createTodo() {
  data.todos.push({
    description: data.todoDescription,
    color: data.todoColor,
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