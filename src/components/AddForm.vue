<script setup>
import Editor from "@tinymce/tinymce-vue";
</script>

<template>
  <div class="add-form">
    <div class="btn-add" @click="showForm = true">
      <div class="add-title">
        <span>{{ this.selectedLanguage === "vn" ? "Thêm mới!" : "New!" }}</span>
      </div>
    </div>
    <div v-if="showForm" class="task-overlay" @click="showForm = false"></div>
    <form v-if="showForm" @submit.prevent="addTask">
      <div class="add-title">
        <h3>{{ this.selectedLanguage === "vn" ? "Nhập tiêu đề:" : "Enter Title:" }}</h3>
        <input type="text" v-model="taskTitle" />
      </div>
      <Editor
        v-model="taskText"
        :init="tinyMCEConfig"
        :api-key="'d45ih7g747dkq0arqf21k00ecjdt5y6nqiqy1b785w92n0of'"
      />
      <div class="due-time">
        <h3>{{ this.selectedLanguage === "vn"? "Nhập hạn:" : "Enter due date:" }}</h3>
        <input type="datetime-local" v-model="taskDeadline" />
      </div>
      <button type="submit">
        {{ this.selectedLanguage === "vn" ? "Xác nhận" : "Submit" }}
      </button>
      <button @click="showForm = false">
        {{ this.selectedLanguage === "vn" ? "Quay lại" : "Back" }}
      </button>
    </form>
  </div>
</template>

<script>
export default {
  props: {
    selectedLanguage: {
      type: String,
    },
  },
  data() {
    return {
      tinyMCEConfig: {
        language: this.selectedLanguage === "vn" ? "vi" : "en",
        resize: false,
        height: "40rem",
      },
      showForm: false,
      taskTitle: "",
      taskText: "",
      taskDeadline: "",
    };
  },
  components: {
    Editor,
  },
  methods: {
    addTask() {
      if (this.taskTitle && this.taskText && this.taskDeadline) {
        this.$emit("task-added", {
          title: this.taskTitle,
          text: this.taskText,
          deadline: this.taskDeadline,
        });
        this.taskTitle = "";
        this.taskText = "";
        this.taskDeadline = "";
        this.showForm = false;
      }
    },
  },
};
</script>

<style lang="scss">
.add-form {
  .btn-add {
    // animation: zoom 4s infinite;
    position: fixed;
    z-index: 30;
    top: 80%;
    right: 4%;
    width: 7rem;
    height: 7rem;
    background: #fff;
    cursor: pointer;
    border: none;
    border-radius: 50%;
    transition: 1s;
    background-image: linear-gradient(
      109.6deg,
      rgba(255, 179, 189, 1) 1.8%,
      rgba(254, 248, 154, 1) 50.6%,
      rgba(161, 224, 186, 1) 100.3%
    );
    &::after {
      content: "";
      position: absolute;
      transform: translate(-50%, -50%);
      height: 10px;
      width: 50%;
      background: white;
      top: 50%;
      left: 50%;
    }
    &::before {
      content: "";
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      background: white;
      height: 50%;
      width: 10px;
    }
    &:hover {
      background-color: black;
      background-image: none;
      &::after,
      &::before {
        background: #fff;
      }
      .add-title {
        z-index: 200;
        visibility: visible;
        left: -18rem;
        transition: 1s;
        &::before {
          visibility: visible;
          left: 15.5rem;
          transition: 1s;
        }
      }
    }
    .add-title {
      animation: zoom 2s infinite;
      width: 15.2rem;
      height: 4.5rem;
      background-color: rgb(0, 0, 0);
      position: absolute;
      top: 1.3rem;
      left: -25rem;
      border-radius: 10px;
      visibility: hidden;
      transition: none;
      text-align: center;
      &::before {
        content: "";
        width: 0;
        height: 0;
        border: 1rem solid transparent;
        border-left-color: black;
        top: 1.2rem;
        position: absolute;
        left: 15rem;
        visibility: hidden;
      }
      span {
        transition: none;
        margin: auto;
        padding: auto;
        font-size: 2.5rem;
        font-weight: 100;
        color: white;
        font-family: "Pacifico", cursive;
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
  }
  form {
    position: fixed;
    z-index: 100;
    width: 80%;
    max-width: 60rem;
    height: 50rem;
    transform: translate(-50%, -50%);
    top: 50%;
    left: 50%;
    border-radius: 5px;
    background-color: #ffc;
    padding: 1rem;
    display: flex;
    flex-direction: column;
    .add-title{
      h3{
        font-family: "Pacifico", cursive;
        font-weight: 100;
        font-size: 1.2rem;
      }
      input{
        width: 98%;
        margin: .2rem 0;
        padding: .5rem;
        border-radius: 5px;
        border: 1px solid black;
      }
    }
    .due-time{
      h3{
        font-size: 1.2rem;
        font-family: "Pacifico", cursive;
        font-weight: 100;
      }
    }
    button {
      margin: .5rem 0;
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
@keyframes zoom {
  0% {
    transform: scale(0.9);
  }
  50% {
    transform: scale(1);
  }
  100% {
    transform: scale(0.9);
  }
}
</style>
