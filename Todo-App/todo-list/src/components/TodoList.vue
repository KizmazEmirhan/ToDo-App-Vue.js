<template>
  <div class="todo-start">
    <div class="header-image">
      <img src="../assets/todo-list-headerImg.jpeg" />
    </div>
    <div class="add-task">
      <input
        type="text"
        class="add-task-input"
        placeholder="Add your task here!"
        v-model="newTask"
        @keyup.enter="addTask(newTask)"
      />
    </div>
    <div class="tasks-section">
      <div class="tasks" v-for="(task, index) in tasks" :key="index">
        <span :class="{ 'task-title': true, hidden: task.editing }">{{
          task.title
        }}</span>
        <input
          v-if="task.editing"
          class="task-item-editing"
          v-model="task.title"
          @keyup.enter="doneUpdateTask(task)"
        />
        <button class="edit-btn" @click="updateTask(task)">Update Task</button>
        <button class="remove-btn" @click="removeTask(index)">
          Remove Task
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Todo-List",
  data() {
    return {
      beforeEditCache: "",
      editing: false,
      defaultTasks: [
        {
          id: 1,
          completed: false,
          title: "todo 1",
          hasCompleted: false,
          editing: false,
        },
        {
          id: 2,
          completed: true,
          title: "todo 2",
          hasCompleted: false,
          editing: false,
        },
      ],
      tasks: [],
      newTask: "",
    };
  },
  mounted() {
    if (localStorage.getItem("tasks")) {
      try {
        this.tasks = JSON.parse(localStorage.getItem("tasks"));
      } catch (e) {
        localStorage.removeItem("tasks");
      }
    } else {
      this.tasks = this.defaultTasks;
    }
  },
  computed: {
    tasksFromLocalStorage() {
      return JSON.parse(localStorage.getItem("tasks") || "[]");
    },
  },
  methods: {
    addTask(newTask) {
      console.log(newTask);
      if (newTask.trim().length == 0) {
        return;
      }
      this.tasks.push({
        id: Date.now(),
        completed: false,
        title: newTask,
        hasCompleted: false,
        editing: false,
      });
      localStorage.setItem("tasks", JSON.stringify(this.tasks));
      this.newTask = "";
    },
    removeTask(index) {
      this.tasks.splice(index, 1);
      localStorage.setItem("tasks", JSON.stringify(this.tasks));
    },
    updateTask(task) {
      this.beforeEditCache = task.title;
      task.editing = true;
    },
    doneUpdateTask(task) {
      if (this.beforeEditCache.trim().length == 0) {
        task.title = this.beforeEditCache;
      }
      const updatedTasks = this.tasks.map((t) =>
        t.id === task.id ? { ...t, title: task.title, editing: false } : t
      );
      localStorage.setItem("tasks", JSON.stringify(updatedTasks));
      task.editing = false;
    },
  },

  created() {
    this.tasks = this.tasksFromLocalStorage;
  },
  props: {
    msg: String,
  },
};
</script>

<style scoped>
@import "../styles/todo-styles.css";
</style>
