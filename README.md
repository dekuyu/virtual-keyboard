## 项目需求
由于工业触控平板端在工厂流水线中无法方便使用外设键盘，需要开发一个虚拟键盘在输入框实现数字、英文以及相关符号的输入，因此实现了一个基于vue3+vite的虚拟键盘，具体实现源码可见源码链接。

## 实现效果
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/7493b2bed891f480daeb1bd1cba3369a.gif)

## 使用
主文件内容
```javascript
<template>
  <div id="app">
    <div>
      <div>
        中文：<input
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

```

### 要想让键盘自动绑定上，input或textarea必须设置keyboard="true"属性

### 源码下载后使用pnpm安装依赖
```bash
pnpm install
```

### input属性

| 属性      | 说明     | 值       | 备注               |
| --------- | -------- | -------- | ------------------ |
| data-mode | 键盘类型 | cn       | 中文               |
| data-mode | 键盘类型 | en_cap   | 英文大写           |
| data-mode | 键盘类型 | en       | 英文小写           |
| data-mode | 键盘类型 | num      | 数字键盘           |
| data-mode | 键盘类型 | biaodian | 符号键盘           |
| data-mode | 键盘类型 | password | 密码键盘           |
| data-mode | 键盘类型 | id_card  | 身份证键盘         |
| data-mode | 键盘类型 | di_git   | 带小数点的数字键盘 |


```html
<input data-mode="en"/>  //通过在input内使用data-mode可以调整键盘显示样式
```

### 词库在本项目的src/dict目录下

### 引用词库示例


### 词库介绍

| 属性          | 说明       | 备注                                  |
| ------------- | ---------- | ------------------------------------- |
| baseDict.json | 单词汇词库 | 基础词库                              |
| address.json  | 多词汇词库 | 全国省市区地址(主要)和一些短词句词库  |
| chowder.json  | 多词汇词库 | 日常生活,成语,人名,全国省市区地址词库 |
| ``     |            |                                       |

### 组件属性

| 属性           | 说明                     | 值            | 备注                           |
| -------------- | ------------------------ | ------------- | ------------------------------ |
| singleDict     | 单词汇词库               | baseDict.json | 要想使用汉字拼写功能此项必填   |
| manyDict       | 多词汇词库               | 请看词库介绍  | 可以根据不同场景使用不同的词库 |
| maxQuantify    | 显示选词个数             | Number        | 默认 10                        |
| showKeyboard   | 手动显示隐藏键盘         | true/false    | 默认不需要自己手动传值         |
| inputEvent     | 手动传入可编辑的对象     | element       | 用来处理可编辑元素             |
| blurHide       | 失去焦点是否自动隐藏键盘 | true/false    |                                |
| transitionTime | 键盘动画过渡时间         | String        | 默认 '0.3s'                    |
| ``      |                          |               |                                |

### 组件事件

| 事件                 | 说明                       | 值           | 备注                                                                                      |
| -------------------- | -------------------------- | ------------ | ----------------------------------------------------------------------------------------- |
| clickNumber          | 点击数字符号触发           | key          |                                                                                           |
| clickKey             | 点击字母空格触发           | key          |                                                                                           |
| del                  | 点击删除触发               | key          | 如果是中文，会返回当前的拼音                                                              |
| changeMode           | 键盘类型改变时触发         | data-mode    |                                                                                           |
| changeShow           | 键盘显示和隐藏触发         | true/false   |                                                                                           |
| initResult           | 词库加载事件               | success/fail | 成功和错误都会返回对应的结果                                                              |
| inputBindKeyboard    | 重新给input绑定键盘        |              | 使用：调用组件里面的inputBindKeyboard方法
| contenteditableInput | 更改可编辑元素是触发的事件 | value        |                                                                                           |
