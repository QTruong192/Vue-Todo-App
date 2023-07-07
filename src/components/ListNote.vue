<script setup>
import Editor from "@tinymce/tinymce-vue";
</script>

<template>
  <div>
    <ul>
      <li
        v-for="(task, index) in tasks"
        :key="index"
        @click="displayTask(index)"
      >
        <div class="task-holder" :class="randomRotateClass()">
          <div class="task-cove"></div>
          <h2>{{ task.title }}</h2>
          <Editor
            :api-key="'d45ih7g747dkq0arqf21k00ecjdt5y6nqiqy1b785w92n0of'"
            :key="index"
            v-model="task.text"
            :init="tinyMCEConfig"
          />
          <div class="task-deadline">
            <h3>{{ selectedLanguage === "vn" ? "Hạn:" : "Due date:" }}</h3>
            <p>{{ formatDeadline(task.deadline) }}</p>
          </div>
          <!-- <button @click="removeTask(index)">
          {{ selectedLanguage === "vn" ? "Xóa bỏ" : "Remove" }}
        </button> -->
        </div>
      </li>
    </ul>

    <div v-if="selectedTask !== null" class="task-overlay" @click="closeTask">
      <div class="task-details">
        <div class="task-title">
          <span>{{ selectedLanguage === "vn" ? "Tiêu đề:" : "Title:" }}</span>
          <h2>{{ selectedTask.title }}</h2>
        </div>
        <div class="task-text">
          <Editor
            :api-key="'d45ih7g747dkq0arqf21k00ecjdt5y6nqiqy1b785w92n0of'"
            :key="selectedTaskIndex"
            v-model="selectedTask.text"
            :init="selectedTaskTinyMCEConfig"
          />
        </div>
        <div class="task-deadline">
          <h3>{{ selectedLanguage === "vn" ? "Hạn:" : "Due date:" }}</h3>
          <p>{{ formatDeadline(selectedTask.deadline) }}</p>
        </div>
        <button @click="removeTask(selectedTaskIndex)">
          {{ selectedLanguage === "vn" ? "Xóa bỏ" : "Remove" }}
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    selectedLanguage: {
      type: String,
    },
    tasks: {
      type: Array,
      required: true,
    },
  },
  data() {
    return {
      tinyMCEConfig: {
        language: this.selectedLanguage === "vn" ? "vi" : "en",
        editable_root: false,
        toolbar: false,
        menubar: false,
        resize: false,
        height: "11rem",
      },
      selectedTaskIndex: null,
      selectedTask: null,
      selectedTaskTinyMCEConfig: {},
    };
  },
  components: {
    Editor,
  },
  methods: {
    removeTask(index) {
      this.$emit("task-removed", index);
    },
    formatDeadline(deadline) {
      const date = new Date(deadline);
      const formattedDate = `${
        date.getMonth() + 1
      }/${date.getDate()}/${date.getFullYear()} ${date.getHours()}:${date.getMinutes()}`;
      return formattedDate;
    },
    displayTask(index) {
      this.selectedTaskIndex = index;
      this.selectedTask = this.tasks[index];
      this.selectedTaskTinyMCEConfig = {
        ...this.tinyMCEConfig,
        height: "40rem",
      };
    },
    closeTask() {
      this.selectedTaskIndex = null;
      this.selectedTask = null;
      this.selectedTaskTinyMCEConfig = {};
    },
    randomRotateClass() {
      const classes = ["rotate-1", "rotate-2", "rotate-3"];
      const randomIndex = Math.floor(Math.random() * classes.length);
      return classes[randomIndex];
    },
  },
};
</script>

<style lang="scss">
ul,
li {
  list-style: none;
}

ul {
  display: flex;
  max-height: 40rem;
  overflow: auto;
  flex-wrap: wrap;
  justify-content: center;
  padding: 3rem;
  li {
    display: flex;
    flex-direction: column;
    margin: 1em;
    cursor: pointer;

    h2 {
      font-weight: 100;
      font-size: 1.5rem;
      max-width: 100%;
      height: 2.5rem;
      overflow: hidden;
      font-family: "Pacifico", cursive;
    }
    p {
      font-size: 1rem;
      font-weight: normal;
    }
    &:hover .task-holder {
      box-shadow: 10px 10px 7px rgba(0, 0, 0, 0.7);
      transform: scale(1.25);
      z-index: 5;
      transition: 0.5s;
    }
    .task-holder {
      transition: 0.5s;
      .task-deadline {
        display: flex;
        align-items: center;
        flex-direction: row;
        justify-content: center;
        height: 2.5rem;
        p {
          padding-left: 0.5rem;
          font-size: 1rem;
        }
        h3 {
          font-size: 1rem;
          font-weight: 100;
          font-family: "Pacifico", cursive;
        }
      }
      .task-cove {
        width: 100%;
        height: 100%;
        position: absolute;
        z-index: 20;
        opacity: 0;
      }
      color: #000;
      background: #ffc;
      display: block;
      height: 15rem;
      width: 15rem;
      padding: 1em;
      box-shadow: 5px 5px 7px rgba(33, 33, 33, 0.7);
      border-radius: 5px;
    }
    .rotate-1 {
      transform: rotate(4deg);
      position: relative;
      top: 5px;
      background: #cfc;
    }
    .rotate-2 {
      transform: rotate(-3deg);
      position: relative;
      top: -5px;
      background: #ccf;
    }
    .rotate-3 {
      transform: rotate(5deg);
      position: relative;
      top: -10px;
    }
  }
}

.task-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.8);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 100;
  .task-details {
    width: 80%;
    max-width: 60rem;
    height: 50rem;
    border-radius: 5px;
    background-color: #ffc;
    padding: 1rem;
    display: flex;
    flex-direction: column;
    .task-title {
      display: flex;
      flex-direction: row;
      align-items: center;
      overflow: hidden;
      span {
        padding: auto;
        font-size: 1.5rem;
        font-weight: 700;
        font-family: "Pacifico", cursive;
      }
      h2 {
        font-weight: 100;
        font-size: 1.5rem;
        max-width: 100%;
        padding: 1rem 1rem;
        font-family: "Pacifico", cursive;
      }
    }
    .task-deadline {
      display: flex;
      align-items: center;
      flex-direction: row;
      justify-content: center;
      height: 2.5rem;
      p {
        padding-left: 0.5rem;
        font-size: 1rem;
      }
      h3 {
        font-size: 1rem;
        font-weight: 100;
        font-family: "Pacifico", cursive;
      }
    }
    button {
      width: auto;
      cursor: pointer;
      padding: 0.3rem 1rem;
      font-size: 1rem;
      font-weight: 300;
      font-family: "Pacifico", cursive;
      border-radius: 5px;
      border: none;
      background-color: #fbab7e;
      background-image: linear-gradient(62deg, #fbab7e 0%, #f7ce68 100%);

      &:hover {
        background-color: black;
        background-image: none;
        color: white;
      }
    }
  }
}
</style>
