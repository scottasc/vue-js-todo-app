<template>
  <div class="home">

    <h2>To do list: {{ calculateIncomplete() }} items left</h2>

    <div>
        <input v-model="newTask.text" placeholder="Enter task..."><br/>
        <button v-on:click="addTask()">Add task</button>
    </div>

    <div>
    <input v-model="taskFilter" list="tasks" placeholder="Search...">
    <datalist id="tasks">
      <option v-for="task in tasks">{{task.text}}</option>
    </datalist>
    </div>

    <div>
      <button v-on:click="setSortAttribute('text')">Sort tasks</button>
    </div>

    <ol>
      <h2><transition-group name="fade"><li v-for="task in orderBy(filterBy(tasks, taskFilter, 'text'), sortAttribute, sortOrder)" v-bind:key="task.id" v-on:click="completeTask(task)" v-bind:class="{strike: task.completed}">{{task.text}}</li></transition-group></h2>
    </ol>

      <div>
        <button v-on:click="removeCompleted()">Delete all completed</button>
      </div>

  </div>
</template>

<style>

.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
}

.fade-enter, .fade-leave-to {
  opacity: 0;
}

body {
  width: 100wh;
  height: 90vh;
  color: white;
  background: linear-gradient(-45deg, #FFFFFF, #003300, #0000AA, #FFFFFF);
  background-size: 400% 400%;
  -webkit-animation: Gradient 15s ease infinite;
  -moz-animation: Gradient 15s ease infinite;
  animation: Gradient 15s ease infinite;
}

@-webkit-keyframes Gradient {
  0% {
    background-position: 0% 50%
  }
  50% {
    background-position: 100% 50%
  }
  100% {
    background-position: 0% 50%
  }
}

@-moz-keyframes Gradient {
  0% {
    background-position: 0% 50%
  }
  50% {
    background-position: 100% 50%
  }
  100% {
    background-position: 0% 50%
  }
}

@keyframes Gradient {
  0% {
    background-position: 0% 50%
  }
  50% {
    background-position: 100% 50%
  }
  100% {
    background-position: 0% 50%
  }
}

h1, h6 {
  font-family: 'Open Sans';
  font-weight: 300;
  text-align: center;
  position: absolute;
  top: 45%;
  right: 0;
  left: 0;
}

  input {
    border: solid;
    padding-bottom: 30px;
    width: 500px;
    font-size: 20px;
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
      newTask: {text: "", completed: false},
      taskFilter: "",
      sortAttribute: "text",
      sortOrder: -1
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
    setSortAttribute: function(inputAttribute) {
      if (this.sortAttribute === inputAttribute) {
        this.sortOrder = this.sortOrder * -1;
      } else {
        this.sortOrder = 1;
      }
      this.sortAttribute = inputAttribute;
    },
    addTask: function() {
        var params = {text: this.newTask.text};

        axios
        .post("http://localhost:3000/api/tasks", params)
        .then(function(response) {
            console.log(response.data);
            this.tasks.push(response.data);
        }.bind(this));
      this.newTask = {text: "", completed: false}; // clearing out the input and creating a new hash in it, with a different memory address, so the hash we just added isn't connected with the previous hash we've added. We had to create a new hash somewhere, or else it nwould have the same memory address.
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