<template>
  <div id="app">
    <h1>Vuejs To-Do List</h1>
    <input type="text" v-model="newTask" placeholder="Enter a new task" @keyup.enter="addTask">
    <button @click="addTask">Add Task</button>
    <ul>
      <li v-for="task in tasks" :key="task.id">
        <input type="checkbox" v-model="task.completed" @change="updateTask(task)">
        <span :class="{ 'completed': task.completed }" v-if="!task.editing" @click="editTask(task)">{{ task.title }}</span>
        <input type="text" v-model="task.updatedTitle" v-else @keyup.enter="saveTask(task)" @blur="saveTask(task)">
        <span>Created on: {{ task.date }}</span>
        <button @click="removeTask(task)">Remove</button>
      </li>
    </ul>
    <h2>Completed Tasks</h2>
    <ul>
      <li v-for="task in completedTasks" :key="task.id">
        <span class="completed">{{ task.title }}</span>
        <span>Created on: {{ task.date }}</span>
      </li>
    </ul>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'App',
  data() {
    return {
      tasks: [],
      newTask: '',
    };
  },
  created() {
    this.fetchTasks();
  },
  methods: {
    fetchTasks() {
      axios.get('http://localhost:3000/tasks')
        .then(response => {
          this.tasks = response.data;
        })
        .catch(error => {
          console.error('Error fetching tasks:', error);
        });
    },
    addTask() {
      if (this.newTask.trim() !== '') {
        const task = {
          title: this.newTask,
          completed: false,
          date: new Date().toLocaleString()
        };
        axios.post('http://localhost:3000/tasks', task)
          .then(response => {
            this.tasks.push(response.data);
            this.newTask = '';
          })
          .catch(error => {
            console.error('Error adding task:', error);
          });
      }
    },
  updateTask(task) {
  axios.put(`http://localhost:3000/tasks/${task.id}`, task)
    .then(() => {
      console.log('Task updated successfully');
    })
    .catch(error => {
      console.error('Error updating task:', error);
    });
},

    editTask(task) {
      task.editing = true;
      task.updatedTitle = task.title;
    },
    saveTask(task) {
      task.title = task.updatedTitle;
      delete task.editing;
      this.updateTask(task);
    },
    removeTask(task) {
  axios.delete(`http://localhost:3000/tasks/${task.id}`)
    .then(() => {
      this.tasks = this.tasks.filter(t => t.id !== task.id);
    })
    .catch(error => {
      console.error('Error deleting task:', error);
    });
  },

  },
  computed: {
    completedTasks() {
      return this.tasks.filter(task => task.completed);
    }
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  width: 100%;
  height: 100%;
  background-color: #252829e7;
}

#app {
  position: sticky;
  width: 90%; 
  max-width: 690px; 
  left: 30%;
  padding: 20px; 
  font-family: Arial, sans-serif;
  text-align: center;
  color: rgba(255, 255, 255, 0.744);
}

#app h1 {
  margin-bottom: 20px; 
}

#app h2 {
  margin-top: 20px; 
}

input[type="text"] {
  padding: 10px;
  margin: 10px 0; 
  width: calc(100% - 135px); 
  font-family: Arial, sans-serif;
}

button {
  padding: 10px 20px;
  border-radius: 5px;
  background-color: #4CAF50;
  color: white;
  border: none;
  cursor: pointer;
  margin: 10px; 
}

button:hover {
  background-color: #45a049;
}

li {
  list-style: none;
  margin: 10px;
  padding: 10px;
  background-color: #f2f2f2;
  border-radius: 5px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  color: black;
}

.completed {
  text-decoration: line-through;
}

/* Media query for smaller screens */
@media screen and (max-width: 800px) {
  #app {
    width: 100%; 
    padding: 10px; 
  }
  input[type="text"] {
  padding: 10px;
  margin: 10px 0; 
  width: calc(100% - 20px);
  font-family: Arial, sans-serif;
}
}
</style>
