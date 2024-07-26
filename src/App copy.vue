<template>
  <div id="app">
    <div>
      <div>
        中文：<input
          id="abc"
          style="width: 400px; height: 25px; font-size: 20px"
          type="text"
          v-model="value"
          keyboard="true"
          @click="stopDefault"
        />
      </div>
      <div>
        英文大写：<input type="text" data-mode="en_cap" keyboard="true" />
      </div>
      <div>英文小写：<input type="text" data-mode="en" keyboard="true" /></div>
      <div>数字：<input type="text" data-mode="num" keyboard="true" /></div>
      <div>
        身份证：<input type="text" data-mode="id_card" keyboard="true" />
      </div>
      <div>浮点：<input type="text" data-mode="di_git" keyboard="true" /></div>
      <div>
        符号：<input type="text" data-mode="biaodian" keyboard="true" />
      </div>
      <div>
        密码键盘：<input type="text" data-mode="password" keyboard="true" />
      </div>

      自动绑定到textarea<textarea type="text" keyboard="true"></textarea>
      <div>
        手动显示输入法<textarea
          type="text"
          @focus="focusInput2"
          @blur="blurInput2"
        ></textarea>
      </div>
      <div>
        <p contenteditable id="elem" @click="clickDiv" @blur="blurInput2">
          div手动显示输入法： 我是可以被编辑的div
        </p>
      </div>
      <div style="height: 400px"></div>
      <keyboard
        :transitionTime="'0.5s'"
        :maxQuantify="10"
        :showKeyboard="showKeyboard"
        @clickKey="clickKey"
        float
        :inputEvent="currentInput"
        @initResult="initFulfil"
        :manyDict="manyDict"
        :singleDict="singleDict"
        @keyboardTips="keyboardTips"
        @clickNumber="clickNumber"
        :blurHide="false"
      ></keyboard>
    </div>
  </div>
</template>

<script>
import keyboard from "./components/keyboard/keyboardIndex.vue";
// import keyboard from "vue-virtual-keyboard-cn/keyboardIndex.vue";
export default {
  name: "App",
  components: {
    keyboard,
  },
  methods: {
    clickNumber(key) {
      console.log("clickNumber", key);
    },
    keyboardTips(txt) {
      console.log("tips", txt);
    },
    initFulfil() {
      // this.loading.close();
    },
    clickDiv(e) {
      console.log("点击", e.target.getAttribute("contenteditable"));
      e.mode = "en_cap";
      this.currentInput = e;
      this.showKeyboard = true;
    },
    focusInput2(e) {
      console.log("聚焦", e.target.getAttribute("contenteditable"));
      e.mode = "en_cap";
      this.currentInput = e;
      this.showKeyboard = true;
    },
    blurInput2() {
      // console.log("失焦", e);
      this.currentInput = null;
      this.showKeyboard = false;
    },
    stopDefault() {},
    clickKey(key) {
      console.log("key", key);
    },
    bodyStopMousedown(e) {
      // debugger;
      // e.stopPropagation();
      e.stopImmediatePropagation();
    },
    cancelOrRecoveryBodyMousedown(typeText) {
      const body = document.querySelector("body");
      let options = { capture: false };
      if (typeText === "cancel") {
        body.addEventListener("mousedown", this.bodyStopMousedown, options);
      } else {
        body.removeEventListener("mousedown", this.bodyStopMousedown, options);
      }
    },
    focusInput() {
      setTimeout(() => {
        this.select.visible = true;
      }, 300);
    },
  },
  mounted() {
    if (window.screen.width < 700) {
      // 当前设备是移动设备
      const inputAll = document.querySelectorAll("[keyboard='true']");
      for (let item of inputAll) {
        item.setAttribute("inputmode", "none");
      }
    }
    new Error("我是错误");
    // this.loading = this.$loading({
    //   lock: true,
    //   text: "词库加载中。。",
    //   spinner: "el-icon-loading",
    //   background: "rgba(0, 0, 0, 0.7)",
    // });
    let elem = document.querySelector("#elem");
    let observer = new MutationObserver((mutationRecords) => {
      console.log(mutationRecords); // console.log(the changes)
    });

    // 观察除了特性之外的所有变动
    observer.observe(elem, {
      childList: true, // 观察直接子节点
      subtree: true, // 及其更低的后代节点
      characterDataOldValue: true, // 将旧的数据传递给回调
    });

    this.cancelOrRecoveryBodyMousedown("cancel");
    this.select = this.$refs.select;
  },
  data() {
    return {
      manyDict: "dict/chowder.json",
      singleDict: "dict/baseDict.json",
      currentInput: "",
      showKeyboard: false,
      value: "",
      value2: "",
    };
  },
};
</script>

<style >
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
