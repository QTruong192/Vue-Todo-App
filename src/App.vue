<script setup>
import AddForm from "./components/AddForm.vue";
import ListNote from "./components/ListNote.vue";
</script>

<template>
  <div id="app">
    <div class="container">
      <div class="wrap-toast">
        <div v-for="(toast, index) in toasts" :key="index" class="toast">
          <div class="toast-content">
            <span>{{ toast }}</span>
          </div>
        </div>
      </div>
      <h1>ToToDo</h1>
      <AddForm
        :selectedLanguage="selectedLanguage"
        @task-added="addTask"
      ></AddForm>
      <ListNote
        :selectedLanguage="selectedLanguage"
        :tasks="tasks"
        @task-removed="removeTask"
      ></ListNote>
      <div class="select-language">
        <h2>{{ this.selectedLanguage === "vn" ? "Ngôn ngữ" : "Language" }}</h2>
        <select v-model="selectedLanguage" @change="updateLanguage">
          <option value="vn">VN</option>
          <option value="en">EN</option>
        </select>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  components: {
    AddForm,
    ListNote,
  },
  data() {
    return {
      showToast: false,
      toastMessage: "",
      selectedLanguage: localStorage.getItem("selectedLanguage") || "vn",
      taskTitle: "",
      taskText: "",
      taskDeadline: "",
      tasks: [],
      toasts: [],
    };
  },
  mounted() {
    this.loadTasks();
    //update thời hạn để cảnh báo mỗi 20s
    setInterval(this.updateTime, 20000);
  },
  watch: {
    tasks: {
      deep: true,
      handler() {
        this.saveTasks();
      },
    },
  },
  methods: {
    showNotification(message) {
      this.toasts.push(message);
      //tắt mỗi khi đạt 15s
      setTimeout(() => {
        this.hideNotification(message);
      }, 15000);
    },
    hideNotification(message) {
      this.toasts = this.toasts.filter((toast) => toast !== message);
    },
    //thêm task
    addTask(task) {
      this.tasks.push(task);
    },
    //xóa task
    removeTask(index) {
      this.tasks.splice(index, 1);
    },
    //lưu task vào localStorage
    saveTasks() {
      localStorage.setItem("tasks", JSON.stringify(this.tasks));
    },
    //load các task trong localStorage
    loadTasks() {
      const savedTasks = localStorage.getItem("tasks");
      if (savedTasks) {
        this.tasks = JSON.parse(savedTasks);
      }
    },
    //thời gian và thời hạn
    updateTime() {
      const now = new Date();
      // Lặp qua các công việc và tính toán thời gian còn lại
      this.tasks.forEach((task) => {
        const deadline = new Date(task.deadline);
        const timeLeft = deadline - now;
        if (timeLeft <= 0) {
          // Thời gian đã hết hạn, thực hiện hành động cần thiết
          // console.log(`Công việc "${task.title}" đã hết hạn!`);
        } else if (timeLeft <= 60 * 60 * 1000) {
          // Còn 1 giờ hoặc ít hơn, hiển thị thông báo
          const message = `${
            this.selectedLanguage === "vn" ? "Công việc" : "Task"
          } ${task.title} ${
            this.selectedLanguage === "vn"
              ? "sắp hết hạn!"
              : "is about to expire!"
          }`;
          this.showNotification(message);
          // console.log(message);
        }
      });
    },
    //load lại trang khi chọn ngôn ngữ
    updateLanguage() {
      localStorage.setItem("selectedLanguage", this.selectedLanguage);
      window.location.reload();
    },
  },
};
</script>

<style lang="scss">
@import url("https://fonts.googleapis.com/css2?family=Pacifico&display=swap");
* {
  margin: 0;
  padding: 0;
  transition: 0.5s;
}
#app {
  width: 100%;
  height: 100%;
}
body,
html {
  width: 100%;
  height: 100%;
  background-image: url(./assets/Cat23_generated.jpg);
  background-size: 100%;
  background-repeat: repeat;
}
.container {
  max-width: 90rem;
  margin: 0 auto;
  .wrap-toast {
    top: 1rem;
    right: 1rem;
    position: fixed;
    display: flex;
    flex-direction: column;
    justify-content: flex-end;
    z-index: 9999;
    max-height: 40rem;
    overflow: hidden;
    .toast {
      margin: .5rem 0;
      .toast-content {
        overflow: hidden;
        max-width: 25rem;
        height: 3.5rem;
        background-color: aliceblue;
        display: flex;
        justify-content: center;
        align-items: center;
        border-radius: 5px;
        padding: 0 1rem;
        box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
        font-family: "Pacifico", cursive;
        font-weight: 100;
        color: rgb(180, 16, 16);
      }
    }
  }
  .select-language {
    position: fixed;
    background-image: linear-gradient(
      109.5deg,
      rgba(229, 233, 177, 1) 11.2%,
      rgba(223, 205, 187, 1) 100.2%
    );
    top: 0;
    left: 0;
    display: flex;
    flex-direction: row;
    padding: 0.2rem 0.5rem;
    border-bottom-right-radius: 10px;
    align-items: center;
    h2 {
      font-family: "Pacifico", cursive;
      font-size: 2rem;
      color: black;
    }
    select {
      margin: 0 0.8rem;
      border-radius: 5px;
      height: 2rem;
      font-size: 1rem;
    }
  }
  h1 {
    text-align: center;
    font-size: 4.5rem;
    font-weight: bold;
    background-color: #fbda61;
    background-image: linear-gradient(45deg, #fbda61 0%, #ff5acd 100%);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    font-family: "Pacifico", cursive;
    padding-top: 2rem;
  }
}

/* Tablet */
@media (max-width: 1023px) {
  html {
    font-size: 80%;
  }
}
/* Mobile */
@media (max-width: 740px) {
  html {
    font-size: 70%;
  }
}
</style>
