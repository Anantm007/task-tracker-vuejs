<template>
  <div v-for="task in tasks" :key="task.id">
    <Task :task="task" @delete-task="$emit('delete-task', task.id)"
    @toggle-reminder="$emit('toggle-reminder', task.id)" />
  </div>
</template>

<script>
import Task from "./Task";

export default {
  name: "Tasks",

  components: {
    Task,
  },

  props: {
    tasks: Array,
  },

  // Get rid of console warning
  emits: {
    emits: ["delete-task", "toggle-reminder"],
  },
};
</script>
