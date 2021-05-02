<template>
  <!-- 
  <div v-if="showAddTask">
    <AddTask @add-task="addTask" />
  </div>
  -->

  <AddTask v-show="showAddTask" @add-task="addTask" />

  <Tasks
    :tasks="tasks"
    @toggle-reminder="toggleReminder"
    @delete-task="deleteTask"
  />
</template>

<script>
import Tasks from "../components/Tasks";
import AddTask from "../components/AddTask";

export default {
  name: "Home",

  // props to expect
  props: {
    showAddTask: Boolean,
  },

  // State
  data() {
    return { tasks: [] };
  },

  // Children Components to render in the component
  components: { Tasks, AddTask },

  // Component methods
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
