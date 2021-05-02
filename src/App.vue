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
      this.tasks = [...this.tasks, task];
    },

    // Toggle reminder property of a specific task object
    async toggleReminder(id) {
      this.tasks = this.tasks.map((task) =>
        task.id === id ? { ...task, reminder: !task.reminder } : task
      );
    },

    // Delete a task by id
    async deleteTask(id) {
      if (confirm("Are you sure?")) {
        this.tasks = this.tasks.filter((task) => task.id !== id);
      }
    },

    // Hide/show add task form
    async toggleAddTask() {
      this.showAddTask = !this.showAddTask;
    },
  },

  // Lifecycle method, similar to useEffect (make requests here, initlise state etc)
  created() {
    this.tasks = [
      {
        id: "1",
        text: "Doctors Appointment",
        day: "March 5th at 2:30pm",
        reminder: true,
      },
      {
        id: "2",
        text: "Meeting with boss",
        day: "March 6th at 1:30pm",
        reminder: true,
      },
      {
        id: "3",
        text: "Food shopping",
        day: "March 7th at 2:00pm",
        reminder: false,
      },
    ];
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
