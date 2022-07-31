<template>
  <form class="row g-3" @submit.prevent="submitForm" id="task-form">
    <div class="col-4">
      <input type="text" class="form-control" placeholder="Title" v-model="task.title">
    </div>
    <div class="col-4">
      <input type="text" class="form-control" placeholder="Description" v-model="task.description">
    </div>
    <div class="col-4">
      <label class="form-check-label" for="completed" style="margin-right: 5px">Completed</label>
      <input type="checkbox" class="form-check-input" v-model="task.completed" id="completed">
    </div>
    <button class="btn btn-outline btn-success" type="submit">Submit</button>
  </form>

  <table class="table table-hover">
    <thead>
    <tr>
      <th scope="col">Title</th>
      <th scope="col">Description</th>
      <th scope="col">Completed</th>
      <th scope="col">Action</th>
    </tr>
    </thead>
    <tbody>
    <tr v-for="task in completedTasks" :key="task.id" class="bg-warning" @dblclick="$data.task = task">
      <td>{{ task.title }}</td>
      <td>{{ task.description }}</td>
      <td>Done</td>
      <td><button class="btn btn-sm btn-outline btn-primary mx-2"  @click="$data.task = task">Edit
        </button>
        <button class="btn btn-sm btn-outline btn-danger"  @click="deleteTask(task)">X
        </button>
      </td>
    </tr>
    <tr v-for="task in uncompletedTasks" :key="task.id" @dblclick="$data.task = task">
      <td>{{ task.title }}</td>
      <td>{{ task.description }}</td>
      <td>In progress</td>
      <td>
        <button class="btn btn-sm btn-outline btn-primary mx-2" @click="$data.task = task">Edit
        </button>
        <button class="btn btn-sm btn-outline btn-danger"  @click="deleteTask(task)">X
        </button>
      </td>
    </tr>
    </tbody>
  </table>
</template>

<script>

export default {
  name: 'App',
  data() {
    return {
      task: {},
      tasks: []
    }
  },
  async created() {
    await this.getTasks()
  },
  methods: {
    submitForm() {
      if (this.task.id === undefined) {
        this.createTask()
      } else {
        this.editTask()
      }
    },
    async getTasks() {
      var response = await fetch('http://127.0.0.1:8000/api/tasks/')
      this.tasks = await response.json();
    },
    async createTask() {
      await this.getTasks()
      await fetch('http://127.0.0.1:8000/api/tasks/', {
        method: 'post',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(this.task)
      });
      await this.getTasks()
      this.task = {};
    },
    async editTask() {
      await this.getTasks()
      await fetch(`http://127.0.0.1:8000/api/tasks/${this.task.id}/`, {
        method: 'put',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(this.task)
      });
      await this.getTasks()
      this.task = {};
    },
    async deleteTask(task) {
      await this.getTasks()
      await fetch(`http://127.0.0.1:8000/api/tasks/${task.id}/`, {
        method: 'delete',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(this.task)
      });
      await this.getTasks()
      this.task = {};
    },
  },

  computed: {
    completedTasks: function () {
      return this.tasks.filter((task) => task.completed)
    },
    uncompletedTasks: function () {
      return this.tasks.filter((task) => !task.completed)
    },
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

</style>
