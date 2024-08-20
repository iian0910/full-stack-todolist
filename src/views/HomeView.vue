<template>
  <div class="d-flex justify-content-center align-items-center" style="height: 100vh" v-if="isLoading">
    <div class="spinner-border text-info" role="status">
      <span class="visually-hidden">Loading...</span>
    </div>
  </div>
  <div class="todo_board" v-else>
    <div class="input-group mb-3">
      <input
        type="text"
        v-model="todo"
        class="form-control"
        placeholder="Add Todo..."
        aria-label="Add Todo..."
        aria-describedby="button-addon2"
      >
      <button
        class="btn btn-outline-secondary"
        type="button"
        id="button-addon2"
        @click="addNewTodo()"
      >
        新增
      </button>
    </div>
    <ul class="list-group list-group-flush">
      <li
        class="list-group-item px-0"
        v-for="todo in todoList"
        :key="todo.id"
      >
        <div>
          <div class="row align-items-center">
            <div class="col-8">
              <input type="text" class="form-control" v-if="isEdit">
              <div v-else>
                {{ todo.description }}
              </div>
            </div>
            <div class="col-4">
              <div class="btn-group">
                <button class="btn btn-outline-primary" @click="updateNote(todo.id)" disabled>
                  更新
                </button>
                <button class="btn btn-outline-danger" @click="deleteNote(todo.id)">
                  刪除
                </button>
              </div>
            </div>
          </div>
        </div>
      </li>
    </ul>
  </div>
</template>

<script setup>
import axios from 'axios'
import { onMounted, ref } from 'vue'

const API_URL = 'https://full-stack-todolist-api.vercel.app'
const todo = ref('')
const isEdit = ref(false)
const isLoading = ref(false)
const todoList = ref([])

const refreshData = async () => {
  // isLoading.value = true
  const { data } = await axios.get(`${API_URL}/api/todo_app/GetNote`)

  if (data.success) {
    // isLoading.value = false
    todoList.value = data.data
  } else {
    console.error(data.message)
  }
}

const addNewTodo = async () => {
  isLoading.value = true
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
  isLoading.value = true
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

<style lang="scss" scoped>
.todo_board {
  width: 450px;
  position: relative;
  border: 1px solid black;
  padding: 20px;
  border-radius: 8px;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
</style>
