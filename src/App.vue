<template>
  <div id="app">
    <div>
      <div>
        中文：<input
          id="elem"
          style="width: 400px; height: 25px; font-size: 20px"
          type="text"
          v-model="value"
          keyboard="true"
        />
      </div>
      <div style="height: 400px"></div>
      <!-- {params: blurHide}：布尔值，ture为输入框失去焦点时自动隐藏键盘 -->
      <keyboard
        :transitionTime="'0.5s'"
        :maxQuantify="10"
        :showKeyboard="showKeyboard"
        @clickKey="clickKey"
        float
        :manyDict="manyDict"
        :singleDict="singleDict"
        @clickNumber="clickNumber"
        :blurHide="false"  
      ></keyboard>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";
import keyboard from "./components/keyboard/keyboardIndex.vue";

const value = ref("");
const showKeyboard = ref(false);

//点击键盘的值
const clickKey = (key) => {
  // console.log("key-->>",key);
}
//点击键盘时数字的值
const clickNumber = (key) => {
  // console.log("key-->>",key);
}
const manyDict = ref("dict/chowder.json")
const singleDict = ref("dict/baseDict.json")
</script>

<style>
.keyDown {
  background: #2c3e50;
}
body {
  margin: 0px;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
