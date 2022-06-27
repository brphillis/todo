<template>
  <div class="container">
    <h2 class="text-center mt-5 mb-5">TO DO LIST</h2>

    <!-- INPUT -->
    <div class="d-flex">
      <input
        v-model="task"
        type="text"
        placeholder="Enter Task"
        class="form-control input-field"
      />
      <button @click="sumbitTask" class="btn btn-primary btn-lg btn-radius">
        SUBMIT
      </button>
    </div>
    <div class="todo-container">
      <div class="task-container" v-for="(task, index) in tasks" :key="index">
        <div class="task-inner-container" v-if="!task.deleted" :class="{ 'display-none' : task.deleted}" >
          <p class="task-description" :class="{ 'complete' : task.completed}">{{task.description}}</p>
          <div class="check-box-container">
            <button class="btn btn-secondary btn-sm" @click="completeTask(task.id)">Complete</button>
          </div>
          <div class="pointer" @click="deleteTask(task.id)">
            <span class="fa fa-trash"></span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
export default {
  name: "TodoList",
  props: {
    msg: String,
  },

  data() {
    return {
      task: " ",
      tasks: [],
    };
  },

  methods: {
    async sumbitTask() {
      if (this.task.length === 0) return;
      await axios({
        method: 'post',
        url: 'http://localhost:3001/create',
        data: {
          description: this.task,
        },
      });
      // get the new list of tasks
      this.getTaskList();
      this.task = "";
    },

    async completeTask(id) {
      await axios({
        method: 'put',
        url: 'http://localhost:3001/complete',
        data: {
          id
        },
      });
      this.getTaskList();
    },

    async deleteTask(id) {
      console.log("what is the id" + id)
      await axios({
        method: 'put',
        url: 'http://localhost:3001/delete',
        data: {
          id
        },
      });
      this.getTaskList();
    },

    async getTaskList() {
      await axios
        .get('http://localhost:3001/')
        .then(response => {
          const { data } = response;
          console.log(JSON.stringify(data));
          this.tasks = data;
      });
    },
  },

  mounted () {
    this.getTaskList();
  },
};
</script>

<style scoped>

.pointer {
  cursor: pointer;
}

.pointer:active {
  color: red;
}

.complete {
  text-decoration: line-through;
  color: gray;
}

.input-field {
  margin-right: 24px;
}

.todo-container {
  box-shadow: 0 1px 3px rgba(0,0,0,0.3);
  border-radius: 6px;
  background-color: 'green';
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  margin-top: 24px;
}

.task-container{
  width: calc(100% - 48px);
}

.task-inner-container {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: 12px;
  margin-bottom: 12px;
  padding-top: 12px;
  padding-bottom: 12px;
  width: calc(100% - 48px);
}

.display-none {
  display: none;
}

.task-description {
  margin-bottom: 0;
}

.check-box-container {
  margin-left: auto;
  margin-right: 24px;
}

.checkbox {
  margin-right: 12px;
}

</style>
