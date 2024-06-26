<template>
  <div class="todo-app">
    <div class="todo-container">
      <div class="add-task-box">
        <h2>Add New Task</h2>
        <div class="add-task">
          <input
            type="text"
            class="add-task-input"
            placeholder="Add your task here!"
            v-model="newTask"
            @keyup.enter="addTask(newTask)"
          />
          <button class="add-btn" @click="addTask(newTask)">
            <span class="material-icons">add</span>
          </button>
        </div>
        <div class="current-date">
          {{ formattedDate }}
        </div>
      </div>
      <div class="tasks-box">
        <h2>Tasks</h2>
        <div class="tasks-section">
          <div
            class="task"
            v-for="(task, index) in tasks"
            :key="index"
            :style="{ backgroundColor: task.color }"
          >
            <span :class="{ 'task-title': true, hidden: task.editing }">
              {{ task.title }}
            </span>
            <input
              v-if="task.editing"
              class="task-item-editing"
              v-model="task.title"
              @keyup.enter="doneUpdateTask(task)"
              ref="editInput"
            />
            <div class="task-icons">
              <button class="icon-btn edit-btn" @click="updateTask(task)">
                <span class="material-icons">edit</span>
              </button>
              <button class="icon-btn remove-btn" @click="removeTask(index)">
                <span class="material-icons">delete</span>
              </button>
            </div>
          </div>
        </div>
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
          color: "#FFB3BA",
        },
        {
          id: 2,
          completed: true,
          title: "todo 2",
          hasCompleted: false,
          editing: false,
          color: "#FFDFBA",
        },
      ],
      tasks: [],
      newTask: "",
      currentDate: new Date(),
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
    formattedDate() {
      const options = { year: "numeric", month: "long", day: "numeric" };
      return this.currentDate.toLocaleDateString(undefined, options);
    },
  },
  methods: {
    addTask(newTask) {
      if (newTask.trim().length == 0) {
        return;
      }
      const pastelColors = [
        "#FFB3BA",
        "#FFDFBA",
        "#FFFFBA",
        "#BAFFC9",
        "#BAE1FF",
        "#D4A5A5",
        "#EAD1DC",
        "#C4A5FF",
        "#A5D4FF",
        "#BAFFD4",
      ];
      const randomColor =
        pastelColors[Math.floor(Math.random() * pastelColors.length)];
      this.tasks.push({
        id: Date.now(),
        completed: false,
        title: newTask,
        hasCompleted: false,
        editing: false,
        color: randomColor,
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
      this.$nextTick(() => {
        this.$refs.editInput.forEach((input, index) => {
          if (this.tasks[index].editing) {
            input.focus();
          }
        });
      });
    },
    doneUpdateTask(task) {
      if (task.title.trim() === "") {
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
<style>
@import "../styles/todo-styles.css";
</style>
