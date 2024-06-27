<template>
  <div
    class="flex flex-col lg:flex-row justify-center items-center min-h-screen p-5 bg-gray-100"
  >
    <div class="w-full max-w-5xl bg-white shadow-lg rounded-lg overflow-hidden">
      <div class="flex flex-col lg:flex-row">
        <div
          class="flex-1 flex flex-col items-center p-5 border-b-2 lg:border-b-0 lg:border-r-2 border-gray-200 bg-teal-50"
        >
          <h2 class="mb-5 text-xl font-bold text-teal-700">Add New Task</h2>
          <div class="flex items-center w-full">
            <input
              type="text"
              class="flex-1 p-4 border border-teal-700 rounded-full text-lg mr-2 focus:outline-none focus:ring-2 focus:ring-teal-700"
              placeholder="Add your task here!"
              v-model="newTask"
              @keyup.enter="addTask(newTask)"
            />
            <button
              class="flex items-center justify-center w-12 h-12 bg-teal-700 rounded-full text-white hover:scale-110 transform transition-transform duration-200"
              @click="addTask(newTask)"
            >
              <span class="material-icons">add</span>
            </button>
          </div>
          <div class="mt-5 text-lg text-teal-700">
            {{ formattedDate }}
          </div>
        </div>
        <div class="flex-2 p-5 bg-green-50 w-full">
          <h2 class="mb-5 text-xl font-bold text-green-800">Tasks</h2>
          <div class="space-y-4">
            <div
              class="flex items-center justify-between p-4 rounded-lg shadow-sm transition-shadow duration-200 transform hover:shadow-md hover:scale-105"
              v-for="(task, index) in tasks"
              :key="index"
              :style="{ backgroundColor: task.color }"
            >
              <span
                :class="{
                  'flex-grow text-lg text-gray-800 mr-2': true,
                  hidden: task.editing,
                }"
              >
                {{ task.title }}
              </span>
              <input
                v-if="task.editing"
                class="w-3/5 p-2 border border-green-800 rounded-lg text-lg"
                v-model="task.title"
                @keyup.enter="doneUpdateTask(task)"
                ref="editInput"
                @keyup.esc="doneUpdateTask(task)"
              />
              <div class="flex space-x-2">
                <button
                  class="flex items-center justify-center w-9 h-9 bg-yellow-500 rounded-full text-white hover:scale-110 transform transition-transform duration-200"
                  @click="updateTask(task)"
                >
                  <span class="material-icons">edit</span>
                </button>
                <button
                  class="flex items-center justify-center w-9 h-9 bg-red-600 rounded-full text-white hover:scale-110 transform transition-transform duration-200"
                  @click="removeTask(index)"
                >
                  <span class="material-icons">delete</span>
                </button>
              </div>
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
@tailwind base;
@tailwind components;
@tailwind utilities;
@import url("https://fonts.googleapis.com/css2?family=Roboto:wght@400;500;700&display=swap");
@import url("https://fonts.googleapis.com/icon?family=Material+Icons");
</style>
