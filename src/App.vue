<template>
  <div class="container">
    <!--"title" is not dynmaic while "tasks" is, hence the v-binding -->

    <Header
      title="Task Tracker"
      @toggle-add-task="toggleAddTask"
      :showAddTask="showAddTask"
    />

    <div v-if="showAddTask">
      <AddTask @add-task="addTask" />
    </div>

    <Tasks
      :tasks="tasks"
      @toggle-reminder="toggleReminder"
      @delete-task="deleteTask"
    />
  </div>
</template>

<script>
import Header from "./components/Header";
import Tasks from "./components/Tasks";
import AddTask from "./components/AddTask";

export default {
  name: "App",

  // Children Components to render in the component
  components: { Header, Tasks, AddTask },

  // State
  data() {
    return { tasks: [], showAddTask: false };
  },

  // Component method
  methods: {
    // Add a new task
    async addTask(task) {
      const res = await fetch("api/tasks", {
        method: "POST",
        headers: {
          "Content-type": "application/json",
        },
        body: JSON.stringify(task),
      });

      const data = await res.json();

      this.tasks = [...this.tasks, data];
    },

    // Toggle reminder property of a specific task object
    async toggleReminder(id) {
      const taskToToggle = await this.fetchTask(id);

      console.log(taskToToggle);

      const updTask = { ...taskToToggle, reminder: !taskToToggle.reminder };

      const res = await fetch(`api/tasks/${id}`, {
        method: "PUT",
        headers: {
          "Content-type": "application/json",
        },
        body: JSON.stringify(updTask),
      });

      const data = await res.json();

      console.log(data);

      this.tasks = this.tasks.map((task) =>
        task.id === id ? { ...task, reminder: data.reminder } : task
      );
    },

    // Delete a task by id
    async deleteTask(id) {
      if (confirm("Are you sure?")) {
        const res = await fetch(`api/tasks/${id}`, {
          method: "DELETE",
        });

        res.status === 200
          ? (this.tasks = this.tasks.filter((task) => task.id !== id))
          : alert("Error!");
      }
    },

    // Hide/show add task form
    async toggleAddTask() {
      this.showAddTask = !this.showAddTask;
    },

    // Fetch all tasks from backend on load
    async fetchTasks() {
      const res = await fetch("api/tasks");

      const data = await res.json();

      return data;
    },

    // Fetch a specific task from backend
    async fetchTask(id) {
      const res = await fetch(`api/tasks/${id}`);

      const data = await res.json();

      return data;
    },
  },

  // Lifecycle method, similar to useEffect (make requests here, initlise state etc)
  async created() {
    this.tasks = await this.fetchTasks();
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Poppins:wght@300;400&display=swap");
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}
body {
  font-family: "Poppins", sans-serif;
}
.container {
  max-width: 500px;
  margin: 30px auto;
  overflow: auto;
  min-height: 300px;
  border: 1px solid steelblue;
  padding: 30px;
  border-radius: 5px;
}
.btn {
  display: inline-block;
  background: #000;
  color: #fff;
  border: none;
  padding: 10px 20px;
  margin: 5px;
  border-radius: 5px;
  cursor: pointer;
  text-decoration: none;
  font-size: 15px;
  font-family: inherit;
}
.btn:focus {
  outline: none;
}
.btn:active {
  transform: scale(0.98);
}
.btn-block {
  display: block;
  width: 100%;
}
</style>
