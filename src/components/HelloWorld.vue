<template>
  <div>
    <h3>Todo App</h3>
    <input id="newNote" v-model="todo"/>&nbsp;
    <button @click="addNewTodo()">Add Note</button>
    <p v-for="todo in todoList" :key="todo.id">
      <b>* {{ todo.description }}</b>
      <button @click="deleteNote(todo.id)">Delete Note</button>
    </p>
  </div>
</template>

<script setup>
import axios from 'axios'
import { onMounted, ref } from 'vue'

const API_URL = 'https://full-stack-todolist-api.vercel.app'
const todo = ref('')
const todoList = ref([])

const refreshData = async () => {
  const { data } = await axios.get(`${API_URL}/api/todo_app/GetNote`)

  if (data.success) {
    todoList.value = data.data
  } else {
    console.error(data.message)
  }
}

const addNewTodo = async () => {
  const fd = new FormData()
  fd.append('newNotes', todo.value)
  const { data } = await axios.post(`${API_URL}/api/todo_app/AddNote`, fd)

  if (data.success) {
    refreshData()
    todo.value = ''
  } else {
    console.error(data.message)
  }
}

const deleteNote = async (id) => {
  const { data } = await axios.delete(`${API_URL}/api/todo_app/DeleteNote?id=${id}`)

  if (data.success) {
    refreshData()
  } else {
    console.error(data.message)
  }
}

onMounted(() => {
  refreshData()
})

</script>
