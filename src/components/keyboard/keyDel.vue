<template>
  <span
    :class="{ 'key def-del': flagStyle, 'key number num-del': !flagStyle }"
    @touchend="touchendDel"
    @touchstart="touchstartDel"
    @mouseup="touchendDel"
    @mousedown="touchstartDel"
  >
    <svg
      viewBox="0 0 1024 1024"
      xmlns="http://www.w3.org/2000/svg"
      :width="kWidhth"
      :height="kHeight"
    >
      <path
        d="M938.8 227.7H284.6L0.1 511.3l284.4 284.4v0.8h654.2c47.1 0 85.3-38.2 85.3-85.3V313c0.1-47.1-38.1-85.3-85.2-85.3z m-172.1 385l-40.2 40.2-100.6-100.6-100.6 100.6-40.2-40.2 100.6-100.6-100.6-100.5 40.2-40.2L625.9 472l100.6-100.6 40.2 40.2-100.6 100.5 100.6 100.6z"
      />
    </svg>
  </span>
</template>

<script setup>
import { inject, ref } from "vue";

const mode = inject("mode");
const flagStyle = ref(true);
let kWidhth = ref("50");
let kHeight = ref("50");
if (!["cn", "en_cap", "en", "password"].includes(mode.value)) {
  flagStyle.value = false;
  kWidhth.value = "65"
  kHeight.value = "65"
}
const interval = ref();
const isIntervalDel = ref(false);
const emits = defineEmits(["del"]);

const touchstartDel = (e) => {
  e.preventDefault();
  if (!interval.value) {
    e.currentTarget.style.background = "#d0d0d0";
    interval.value = setInterval(() => {
      isIntervalDel.value = true;
      emits("del", e);
    }, 100);
  }
};

const touchendDel = (e) => {
  e.preventDefault();

  clearInterval(interval.value);
  if (!isIntervalDel.value) {
    emits("del", e);
  }
  e.currentTarget.style.background = "#fff";
  isIntervalDel.value = false;
};
</script>

<style lang="scss" scoped>
/* 样式代码 */
@import "./index.scss";
</style>
