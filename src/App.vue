<script setup>
  import {onMounted, ref} from 'vue'
  import axios from 'axios'

  const API_URL = 'http://localhost:4000/'

  const tasks = ref([])
  onMounted(() => {
    axios.get(API_URL + 'tasks/')
    .then(response => {
      tasks.value = response.data
      console.log(response.data)
    })
    .catch(error => {
      console.log(error)
    })
  })

  const todo = ref('')
  const status = ref('todo')
  function addTask() {
    if (todo.value) {
      axios.post(API_URL + 'tasks/', {
        todo: todo.value,
        status: status.value
      })
      .then(response => {
        todo.value = ''
        tasks.value.push(response.data)
        console.log(response)
      })
      .catch(error => {
        console.log(error)
      })
    }
  }

  function setTaskStatus(task_id, status) {
    const task = tasks.value.filter(task => task.id === task_id)[0]
    const todo = task.todo

    axios.put(API_URL + `tasks/${task_id}`, {
      todo: todo,
      status: status
    })
    .then(() => {
      task.status = status
    })
    .catch(error => {
      console.log(error)
    })
  }
  
  function deleteTask(task_id) {
    axios.delete(API_URL + `tasks/${task_id}`)
    .then(() => {
      let i = tasks.value.map(data => data.id).indexOf(task_id);
      tasks.value.splice(i, 1);
    })
    .catch(error => {
      console.log(error)
    })
  }
</script>

<template>
  <div class="home mx-auto px-3" style="max-width: 500px;">
    <h1 class="title mt-5 text-center">Tasks</h1>

    <div>
      <div class="is-3 is-offset-3">
        <form v-on:submit.prevent="addTask">
          <div class="input-group mb-3 control">
            <input v-model="todo" type="text" class="form-control" placeholder="Task" required>
            <button class="btn btn-outline-primary">Submit</button>
          </div>
        </form>
      </div>
    </div>

    <div class="card">
      <ul class="list-group list-group-flush">
        <div v-for="task in tasks" v-bind:key="task.id">
          <div v-if="task.status === 'todo'">
            <li class="list-group-item text-center text-capitalize">{{ task.todo }}
              <button @click="setTaskStatus(task.id, 'done')"  class="btn btn-outline-primary position-absolute top-0 end-0" type="button">
                Done
              </button>
            </li>
          </div>

          <div v-if="task.status === 'done'">
            <li class="list-group-item text-decoration-line-through text-center text-capitalize">{{ task.todo }}
              <button @click="setTaskStatus(task.id, 'todo')"  class="btn btn-outline-secondary position-absolute top-0 end-0" type="button">
                Not Done
              </button>

              <button @click="deleteTask(task.id)"  class="btn btn-outline-danger position-absolute top-0 start-0" type="button">
                Delete
              </button>
            </li>
          </div>
        </div>
      </ul>
    </div>

  </div>
</template>