<template>
  <div class="home">
    <h1>To do list: {{ calculateIncomplete() }} items left</h1>

    <div>
        <input v-model="newTask.text"><br/>
        <button v-on:click="addTask()">Add task</button>
    </div>


    <ol>
      <h2><li v-for="task in tasks" v-on:click="completeTask(task)" v-bind:class="{strike: task.completed}">{{task.text}}</li></h2>
    </ol>

      <div>
        <button v-on:click="removeCompleted()">Delete all completed</button>
      </div>

  </div>
</template>

<style>

  input {
    border: solid;
    padding-bottom: 100px;
    width: 500px;
    font-size: 75px;
  }

  h1, h2, input {
    color: darkgreen;
  }

  body {
    background-color: lightgray;
      }

  .strike {
    text-decoration: line-through;
  }

  button {
    background-color: #1AAA99;
    border: solid;
    color: darkgreen;
    padding: 15px 32px;
    text-align: center;
    text-decoration: none;
    display: inline-block;
    font-size: 16px;
     width: 500px;
  }

</style>

<script>
var axios = require('axios');

export default {
  data: function() {
    return {
      tasks: [],
      newTask: {text: "", completed: false}
    };
  },
  created: function() {
    axios
    .get("http://localhost:3000/api/tasks")
    .then(function(response) {
      this.tasks = response.data; // this points to an undefined place because it's in an anonymous function and nested away from a direct reference to the Vue object.
    }
    .bind(this));
  },
  methods: {
    addTask: function() {
      if (this.newTask.text !== "") {
      this.tasks.push(this.newTask);
      this.newTask = {text: "", completed: false}; // clearing out the input and creating a new hash in it, with a different memory address, so the hash we just added isn't connected with the previous hash we've added. We had to create a new hash somewhere, or else it nwould have the same memory address.
     }
    },
    completeTask: function(inputTask) {
      inputTask.completed = !inputTask.completed;
    },
    calculateIncomplete: function() {
      var count = 0;
      this.tasks.forEach(function(task) {
        if (task.completed === false) {
          count++;
        }
      });
      return count;
    },
    removeCompleted: function() {
      var incompleteTasks = [];
      for (var i = 0; i < this.tasks.length; i++) {
        var task = this.tasks[i];

        if (!task.completed) {
          incompleteTasks.push(task);
       }
     }
     this.tasks = incompleteTasks;
   }
 },
  computed: {}
};
</script>