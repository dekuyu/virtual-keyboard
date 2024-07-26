<template>
  <transition :name="!transitionTime ? '' : 'keyboardTransition'">
    <div
      v-show="show"
      :class="['my-keyboard', equipmentType]"
      @mousedown.prevent="mousedown"
      :style="{ transition: `all ${transitionTime}` }"
    >
      <div
        v-if="mode === 'cn' && !showDiction && cn_input?.length"
        class="pinyin"
      >
        <div>
          <span>{{ cn_input }}</span>
        </div>
      </div>
      <div
        v-if="mode === 'cn' && !showDiction && cn_input?.length"
        class="select-list"
        :style="{ height: cut_cn_list.length ? '' : '40px' }"
      >
        <div>
          <div class="item_main">
            <span
              class="select-text"
              v-for="(text, index) in cut_cn_list"
              :key="index"
              @click="clickCN($event, text)"
              >{{ index + 1 + "." + text }}</span
            >
          </div>

          <div class="page" v-if="equipmentType === 'pc'">
            <p :style="previousStyleFn" @[keyEvent]="previous_page">
              <fullTriangle></fullTriangle>
            </p>
            <p :style="nextPageStyleFn" class="next" @[keyEvent]="next_page">
              <fullTriangle></fullTriangle>
            </p>
          </div>
          <div class="page" v-else>
            <p class="next" @[keyEvent]="isShowDiction">
              <fullTriangle></fullTriangle>
            </p>
          </div>
        </div>
      </div>
      <!-- 12键盘 -->
      <div
        v-if="['id_card', 'di_git', 'biaodian', 'num'].includes(mode)"
        :class="[
          'main-keyboard',
          mode != 'biaodian' && old_mode === '' ? 'no_del_box' : '',
        ]"
      >
        <div :class="['number-box', mode]">
          <div
            v-if="mode != 'biaodian' && old_mode === ''"
            @click="HideKey"
            class="hide12key"
          >
            <svg
              width="40"
              height="40"
              viewBox="0 0 48 48"
              fill="none"
              xmlns="http://www.w3.org/2000/svg"
            >
              <path
                d="M36 18L24 30L12 18"
                stroke="rgb(141 141 141)"
                stroke-width="4"
                stroke-linecap="round"
                stroke-linejoin="round"
              />
            </svg>
          </div>
          <template v-for="(key, index) in number_keys2">
            <span
              v-if="
                (index != number_keys2.length - 1 ||
                  old_mode != '' ||
                  mode === 'biaodian') &&
                key != ''
              "
              :key="'key' + index"
              class="key number"
              @[keyEvent]="clickNumber($event, key)"
              :class="{ disabled_key: key === '' ? true : false }"
              >{{ key }}</span
            >
          </template>
          <keyDel
            @del="del"
            v-if="mode != 'biaodian' && old_mode === ''"
          ></keyDel>
        </div>

        <div class="del-box" v-if="mode === 'biaodian' || old_mode != ''">
          <keyDel @del="del"></keyDel>
          <span class="key number" @[keyEvent]="cn_change('cn')">
            <template v-if="mainMode != 'noCn'">中/</template>英</span
          >
          <span
            class="key key_hide number"
            style="margin-left: 0px"
            @[keyEvent]="HideKey"
          >
            <svg
              class="jp"
              viewBox="0 0 1024 1024"
              xmlns="http://www.w3.org/2000/svg"
              v-if="equipmentType === 'pc'"
            >
              <path
                d="M390.94044445 560.84859262h97.39377777V463.45481485H390.94044445v97.39377777zM560.24177778 317.81925929H463.75822222v97.39377778h97.39377778V317.81925929z m-145.63555556 0H318.12266667v97.39377778h97.39377778V317.81925929z m291.27111111 0H609.39377778v97.39377778h97.39377777V317.81925929zM536.576 560.84859262h97.39377778V463.45481485H536.576v97.39377777zM268.97066667 317.81925929H172.48711111v97.39377778h97.39377778V317.81925929z m486.05866666 97.39377778h97.39377778V317.81925929H755.02933333v97.39377778z m145.63555556-243.02933334H123.33511111c-53.70311111 0-97.39377778 43.69066667-97.39377778 97.39377778V754.72592595c0 53.70311111 43.69066667 97.39377778 97.39377778 97.39377778h776.41955556c53.70311111 0 97.39377778-43.69066667 97.39377778-97.39377778V269.57748151c0-53.70311111-43.69066667-97.39377778-96.48355556-97.39377778z m48.24177778 582.54222222c0 26.39644445-21.84533333 48.24177778-48.24177778 48.24177778H123.33511111c-26.39644445 0-48.24177778-21.84533333-48.24177778-48.24177778V269.57748151c0-26.39644445 21.84533333-48.24177778 48.24177778-48.24177778h776.41955556c26.39644445 0 48.24177778 21.84533333 48.24177778 48.24177778l0.91022222 485.14844444zM682.21155555 560.84859262h97.39377778V463.45481485H682.21155555v97.39377777z m-388.66488888 145.63555556h436.90666666V609.0903704H293.54666667v97.39377778zM341.78844445 463.45481485H245.30488889v97.39377777h97.39377778V463.45481485z"
              />
            </svg>
            <span>
              {{ equipmentType === "pc" ? "隐藏" : "隐藏键盘" }}
              <template v-if="equipmentType === 'pc'">
                <br />
                <i style="display: block; transform: scaleX(2)">v</i>
              </template>
            </span>
          </span>
          <span class="key number" @[keyEvent]="Fanhui()">返回</span>
        </div>
      </div>
      <!-- 26键盘 -->
      <div
        v-if="['cn', 'en_cap', 'en', 'password'].includes(mode) && !showDiction"
        class="main-keyboard"
      >
        <template v-if="equipmentType === 'pc'">
          <span
            class="key"
            v-for="(key, index) in number_keys"
            :key="index + 50"
            @[keyEvent]="clickNumber($event, key)"
            >{{ key }}</span
          >
          <br />
        </template>
        <span
          class="key letter"
          v-for="(key, index) in letter_keys.slice(0, 10)"
          :key="index + 11"
          @[keyEvent]="clickKey($event, key)"
          >{{ key }}</span
        >
        <br />
        <span
          class="key letter"
          v-for="(key, index) in letter_keys.slice(10, 19)"
          :key="index + 21"
          @[keyEvent]="clickKey($event, key)"
          >{{ key }}</span
        >
        <br />
        <!-- 此处修改了 v-if="mode === 'cn' && mode != 'password'" -->
        <span
          v-if="mode === 'cn' || mode === 'password'"
          @[keyEvent]="cn_change('en')"
          class="key blue"
        >
          中 /
          <i class="blue_default">英</i>
        </span>
        <span v-else @[keyEvent]="cn_change('cn')" class="key blue">
          英<template v-if="mainMode != 'noCn'">
            /
            <i class="blue_default">中</i>
          </template>
        </span>

        <span
          class="key letter"
          v-for="(key, index) in letter_keys.slice(19, 26)"
          :key="index + 31"
          @[keyEvent]="clickKey($event, key)"
          >{{ key }}</span
        >
        <keyDel @del="del"></keyDel>
        <br />
        <span
          class="key key_hide"
          style="width: 140px; margin-left: 10px"
          @[keyEvent]="HideKey"
        >
          <svg
            class="jp"
            viewBox="0 0 1024 1024"
            xmlns="http://www.w3.org/2000/svg"
            v-if="equipmentType === 'pc'"
          >
            <path
              d="M390.94044445 560.84859262h97.39377777V463.45481485H390.94044445v97.39377777zM560.24177778 317.81925929H463.75822222v97.39377778h97.39377778V317.81925929z m-145.63555556 0H318.12266667v97.39377778h97.39377778V317.81925929z m291.27111111 0H609.39377778v97.39377778h97.39377777V317.81925929zM536.576 560.84859262h97.39377778V463.45481485H536.576v97.39377777zM268.97066667 317.81925929H172.48711111v97.39377778h97.39377778V317.81925929z m486.05866666 97.39377778h97.39377778V317.81925929H755.02933333v97.39377778z m145.63555556-243.02933334H123.33511111c-53.70311111 0-97.39377778 43.69066667-97.39377778 97.39377778V754.72592595c0 53.70311111 43.69066667 97.39377778 97.39377778 97.39377778h776.41955556c53.70311111 0 97.39377778-43.69066667 97.39377778-97.39377778V269.57748151c0-53.70311111-43.69066667-97.39377778-96.48355556-97.39377778z m48.24177778 582.54222222c0 26.39644445-21.84533333 48.24177778-48.24177778 48.24177778H123.33511111c-26.39644445 0-48.24177778-21.84533333-48.24177778-48.24177778V269.57748151c0-26.39644445 21.84533333-48.24177778 48.24177778-48.24177778h776.41955556c26.39644445 0 48.24177778 21.84533333 48.24177778 48.24177778l0.91022222 485.14844444zM682.21155555 560.84859262h97.39377778V463.45481485H682.21155555v97.39377777z m-388.66488888 145.63555556h436.90666666V609.0903704H293.54666667v97.39377778zM341.78844445 463.45481485H245.30488889v97.39377777h97.39377778V463.45481485z"
            />
          </svg>
          <span>
            {{ equipmentType === "pc" ? "隐藏" : "隐藏键盘" }}
            <template v-if="equipmentType === 'pc'">
              <br />
              <i style="display: block; transform: scaleX(2)">v</i>
            </template>
          </span>
        </span>

        <span
          v-if="mode === 'en_cap'" 
          class="key blue case"
          @[keyEvent]="cap_change()"
        >
          {{ equipmentType === "pc" ? "已锁定大写" : "大写" }}
        </span>
        <span v-else class="key blue case" @[keyEvent]="cap_change()">{{
          equipmentType === "pc" ? "切换大写" : "小写"
        }}</span>
        <span class="key space" @[keyEvent]="clickKey($event, ' ', true)"
          >空格</span
        >
        <span @[keyEvent]="() => (mode = 'biaodian')" class="key blue"
          >符号</span
        >
        <span @[keyEvent]="() => (mode = 'num')" class="key blue"
          >数字</span
        >
      </div>
      <!--选词板-->
      <div v-if="mode === 'cn' && showDiction" class="main-keyboard">
        <div class="number-box select_cn">
          <div class="select_cn_main">
            <span
              class="item"
              :key="index"
              v-for="(item, index) in cn_list_str"
              @click="clickCN($event, item)"
            >
              {{ item }}
            </span>
          </div>
        </div>
        <div class="del-box">
          <keyDel @del="del"></keyDel>
          <span
            class="key number blue"
            @[keyEvent]="Fanhui()"
            style="font-size: 36px"
            >返回</span
          >
          <span
            class="key key_hide number"
            style="margin-left: 0px"
            @[keyEvent]="HideKey"
          >
            <svg
              class="jp"
              viewBox="0 0 1024 1024"
              xmlns="http://www.w3.org/2000/svg"
              v-if="equipmentType === 'pc'"
            >
              <path
                d="M390.94044445 560.84859262h97.39377777V463.45481485H390.94044445v97.39377777zM560.24177778 317.81925929H463.75822222v97.39377778h97.39377778V317.81925929z m-145.63555556 0H318.12266667v97.39377778h97.39377778V317.81925929z m291.27111111 0H609.39377778v97.39377778h97.39377777V317.81925929zM536.576 560.84859262h97.39377778V463.45481485H536.576v97.39377777zM268.97066667 317.81925929H172.48711111v97.39377778h97.39377778V317.81925929z m486.05866666 97.39377778h97.39377778V317.81925929H755.02933333v97.39377778z m145.63555556-243.02933334H123.33511111c-53.70311111 0-97.39377778 43.69066667-97.39377778 97.39377778V754.72592595c0 53.70311111 43.69066667 97.39377778 97.39377778 97.39377778h776.41955556c53.70311111 0 97.39377778-43.69066667 97.39377778-97.39377778V269.57748151c0-53.70311111-43.69066667-97.39377778-96.48355556-97.39377778z m48.24177778 582.54222222c0 26.39644445-21.84533333 48.24177778-48.24177778 48.24177778H123.33511111c-26.39644445 0-48.24177778-21.84533333-48.24177778-48.24177778V269.57748151c0-26.39644445 21.84533333-48.24177778 48.24177778-48.24177778h776.41955556c26.39644445 0 48.24177778 21.84533333 48.24177778 48.24177778l0.91022222 485.14844444zM682.21155555 560.84859262h97.39377778V463.45481485H682.21155555v97.39377777z m-388.66488888 145.63555556h436.90666666V609.0903704H293.54666667v97.39377778zM341.78844445 463.45481485H245.30488889v97.39377777h97.39377778V463.45481485z"
              />
            </svg>
            <span>
              {{ equipmentType === "pc" ? "隐藏" : "隐藏键盘" }}
              <template v-if="equipmentType === 'pc'">
                <br />
                <i style="display: block; transform: scaleX(2)">v</i>
              </template>
            </span>
          </span>
          <span
            class="key number blue"
            style="font-size: 36px; visibility: hidden"
            >英文</span
          >
        </div>
      </div>
    </div>
  </transition>
</template>
<script setup>
import { computed, nextTick, onMounted, provide, ref, watch } from "vue";
import AllKey from "./key";
import keyDel from "./keyDel.vue";
import fullTriangle from './fullTriangle.vue'
import axios from 'axios'

//从父组件传来的属性
const props = defineProps({
  manyDict: {
    type: String,
    default: "",
  },
  singleDict: {
    type: String,
    default: "",
  },
  screen: { type: Number, default: 700 },
  blurHide: { type: Boolean, default: true },
  EnterActiveClass: { type: String, default: "fadeInUp" },
  LeaveActiveClass: { type: String, default: "fadeOutDown" },
  showKeyboard: { type: Boolean, default: false },
  maxQuantify: { type: Number, default: 10 },
  transitionTime: { type: String, default: "0.3s" },
  keyUpClass: { type: String, default: "keyUp" },
  keyDownClass: { type: String, default: "keyDown" },
  inputEvent: null,
});

const emits = defineEmits([
  "initResult",
  "success",
  "fail",
  "changeMode",
  "contenteditableInput",
  "clickKey",
  "clickNumber",
  "clickCN",
  "del"
]);

//声明变量
const show = ref(props.showKeyboard);
const showDiction = ref(false);
const equipmentType = ref("pc");
const def_mode = ref("cn");
const old_mode = ref("");
const mainMode = ref("");
const cn_input = ref("");
const cn_list_str = ref([]);
const l_min = ref(0);
const l_max = ref(props.maxQuantify);
const cursorPosition = ref(-1);
//判断当前设备大小
if (window.screen.width < props.screen) {
  //当前设备为移动设备
  equipmentType.value = "phone";
}
let promiseList = [];
let dict = {};
//如果基础词典有值
if (props.singleDict) {
  // const promise = import("../../dict/baseDict.json").then((res) => {
  //   dict = res;
  //   console.log('dictres-->>',res);
  //   // Object.freeze(dict);
  // });
  //部署时将本地的字母表放入了public文件夹
  const promise = axios.get('/dict/baseDict.json').then((res) => {
    dict = res.data
  })
  promiseList.push(promise);
}
//如果存在拓展词典值
let doubleSpell = {};
if (props.manyDict) {
  //@ts-ignore
  // const promise = import("../../dict/chowder.json").then((res) => {
  //   doubleSpell = res;
  //   console.log('doubleSpellres-->>',res);
  //   // Object.freeze(doubleSpell);
  // });
  //部署时将本地的字母表放入了public文件夹
  const promise = axios.get('/dict/chowder.json').then((res) => {
    doubleSpell = res.data
  })
  promiseList.push(promise);
}
Promise.all(promiseList)
  .then(() => {
    emits("initResult", "success");
  })
  .catch((err) => {
    console.error("词库错误", err);
    emits("initResult", "fail");
  });

//数字键盘
const number_keys = computed(() => {
  let list = AllKey.number || [];
  return list.filter((num) => !isNaN(Number(num)));
});

//触发事件
const keyEvent = computed(() => {
  let result = "mousedown";
  if (
    equipmentType.value === "phone" ||
    "ontouchstart" in document.documentElement
  ) {
    result = "touchstart";
  }
  return result;
});

//样式
const previousStyleFn = computed(() => {
  let result = { background: "#a7a7a7" };
  if (l_min.value > 0) {
    result.background = "#344a5d";
  }
  return result;
});
const nextPageStyleFn = computed(() => {
  let result = { background: "#a7a7a7" };
  if (cn_list_str.value.length > l_max.value) {
    result.background = "#344a5d";
  }
  return result;
});

//数字键盘
const number_keys2 = computed(() => {
  let numKeyList = AllKey.number;
  let biaodianKeyList = AllKey.biaodian;

  let resultArray = numKeyList;
  if (["num", "id_card", "di_git"].includes(mode.value)) {
    if (!old_mode.value) {
      resultArray = numKeyList.map((key) => {
        if (["X", "."].includes(key) && mode.value === "num") {
          return "";
        }
        if (["."].includes(key) && mode.value === "id_card") {
          return "";
        }
        if (["X", "."].includes(key) && mode.value === "di_git") {
          if (key === ".") {
            return "";
          }
          return ".";
        }
        return key;
      });
    }
  } else {
    resultArray = biaodianKeyList;
  }
  return resultArray;
});

//英文按键
const letter_keys = computed(() => {
  return mode.value === "en_cap" ? AllKey.cap_letter : AllKey.letter;
});

const cut_cn_list = computed(() => {
  if (cn_list_str.value) {//如果存在中文列表
    let result = cn_list_str.value.slice(l_min.value, l_max.value);//截取中文列表
    if (equipmentType.value === "phone") {
      result = cn_list_str.value;
    }
    return result;//返回截取后的中文列表
  }
  return [];
});

//修改模式
const mode = computed({
  get() {
    return def_mode.value;
  },
  set(val) {
    if (JSON.stringify(dict) === "{}") {
      if (["cn", "password"].includes(val)) {
        val = "password";
      }
      mainMode.value = "noCn";
    }
    if (["password"].includes(val)) {
      mainMode.value = "noCn";
    }
    old_mode.value = def_mode.value;
    def_mode.value = val;
    if (val !== "cn") {
      cn_list_str.value = [];
      cn_input.value = "";
    }
    initKeyColor();
    showDiction.value = false;
    emits("changeMode", val);
  },
});
provide('mode', mode);
//初始化键盘颜色
const initKeyColor = () => {
  nextTick(() => {
    let keyDivList = document.querySelectorAll(
      ".my-keyboard .key:not([bindtouchendAndmouseup])"
    );

    let fn = function (e) {
      if (!Array.from(e.currentTarget.classList).includes("key_hide")) {
        e.currentTarget.style.background = "#fff";
      }
    };
    for (let item of keyDivList) {
      item.setAttribute("bindtouchendAndmouseup", "true");
      item.addEventListener("touchend", fn);
      item.addEventListener("touchmove", fn);
      item.addEventListener("mouseup", fn);
      item.addEventListener("mousemove", fn);
    }
  });
};

//是否显示键盘
const isShowDiction = (e) => {
  e.preventDefault();
  showDiction.value = true;
};
//输入绑定键盘
const inputBindKeyboard = () => {
  nextTick(() => {
    //每个input添加事件
    const inputAll = document.querySelectorAll(
      "[keyboard='true']:not([bindkeyboard])"
    );
    inputAll.forEach((e) => {
      e.setAttribute("bindkeyboard", "true");
      e.addEventListener("focus", showKeyBoardFn);
      e.addEventListener("click", () => {
        setTimeout(() => {}, 100);
      });
      if (props.blurHide) {
        e.addEventListener("blur", () => {
          show.value = false;
        });
      }
      // }
    });
  });
};

let input = {};
const showKeyBoardFn = (e) => {
  e.preventDefault();
  mainMode.value = "";
  if (props.showKeyboard) return;
  input = e.target;
  show.value = true;
  mode.value = e.target.dataset.mode || "cn";
  old_mode.value = "";
};

//设置输入值
const setInputValue = (key, type = "set") => {
  let cursor = input.selectionStart; //光标初始位置
  let tagName = input.tagName;

  let isContenteditable = !!input.getAttribute("contenteditable");
  let value = input.value;
  if (isContenteditable) {
    cursor = getCaretPosition(input);
    value = input.innerText;
  }
  if (type === "del") {
    if (cursor > 0) {
      let cursorEnd = input.selectionEnd;
      if (cursorEnd != cursor && tagName === "INPUT ") {
        value = value
          ?.split("")
          ?.filter((item, index) => {
            if (index < cursor || index >= cursorEnd) {
              return item;
            }
          })
          ?.join("");
      } else {
        value = delStringLast(value, cursor);
        cursor -= 1;
      }
    }
  } else {
    value = insertString(value, key, cursor);
    if (key.charCodeAt(key) > 127 || key.charCodeAt(key) > 94) {
      cursor += key.length;
    } else {
      cursor += 1;
    }
  }

  if (isContenteditable) {
    input.innerText = value;
    let range = document.createRange();
    let sel = window.getSelection();
    range.setStart(input.childNodes[0], cursor);
    range.collapse(true);
    sel?.removeAllRanges();
    sel?.addRange(range);
    emits("contenteditableInput", input.innerText);
  } else {
    input.value = value;
    input.selectionStart = cursor;
    input.selectionEnd = cursor;
    input.dispatchEvent(new Event("input", { bubbles: true }));
  }
};

//获取光标位置
const getCaretPosition = (element) => {
  let carePos = 0, 
    sel,
    range;

  if (window.getSelection) {
    sel = window.getSelection();
    if (sel?.rangeCount) {
      range = sel.getRangeAt(0);

      if (range.commonAncestorContainer.parentNode == element) {
        carePos = range.endOffset;
        if (
          range.commonAncestorContainer
          // range.commonAncestorContainer.length !=
          // range.commonAncestorContainer.data.trim().length
        ) {
          carePos -= 1;
        }
      }
    }
  }

  cursorPosition.value = carePos;
  return carePos;
};

/**删除字符串的某个字符*/
const delStringLast = (text, index) => {
  if (index < 0) return;
  let arrText = text.split("");
  if (index > 0) {
    arrText[index - 1] = "";
  } else {
    arrText[index] = "";
  }

  arrText = arrText.filter((item) => item);
  const endIndex = arrText.length - 1;
  if (arrText[endIndex] === "'") {
    arrText[endIndex] = "";
  }
  let result = arrText.join("");

  return result;
};
/**字符串插入文字 */
const insertString = (text, input, index) => {
  let arrText = text.split("");
  arrText.splice(index, 0, input);
  return arrText.join("");
};

//隐藏
const HideKey = () => {
  show.value = false;
  input.blur();
};

//鼠标按下时
const mousedown = (e) => {
  e.stopImmediatePropagation();
};

//点击键盘时
const clickKey = (e, key, pass) => {
  e.preventDefault(); //阻止默认事件
  e.stopImmediatePropagation(); //阻止冒泡
  e.target.style.background = "#d0d0d0"; //点击时改变背景色
  if (input !== document.activeElement) return; 
  if (mode.value === "cn" && !pass) { //如果是中文模式
    cn_input.value += key;  //拼音输入框添加拼音
    const specialPinYin = ["u", "v", "i"].includes(cn_input.value.split("")[0]); //判断是否是特殊拼音
    if (specialPinYin) {  //如果是特殊拼音
      if (!cn_list_str.value.length) {  //如果没有拼音列表
        //@ts-ignore
        cn_list_str.value = [key]; //拼音列表添加拼音
      } else {
        const value = cn_list_str.value[0] + key; //拼音列表第一个拼音加上当前拼音
        //@ts-ignore
        cn_list_str.value[0] = value; //拼音列表第一个拼音替换为新的拼音
      }
      return;
    }
    findChinese("add", key); //查找中文
  } else {
    setInputValue(key); //设置输入值
  }
  emits("clickKey", key);
};

const findChinese = (type, key) => {
  // type = del key不需要传，type = add key必须要传
  l_min.value = 0; //最小值
  l_max.value = props.maxQuantify;  //最大值
  const pinYinList = cn_input.value.split("'"); //拼音列表
  let pinYin = pinYinList[pinYinList.length - 1]; //当前拼音

  let re = new RegExp(`^${pinYin}\\w*`); //正则,匹配当前拼音开头的拼音
  let keys = Object.keys(dict)  //获取拼音字典的key
    .filter((item) => {  //过滤
      let strList = pinYin.split(""); //当前拼音转数组

      const temp = ["h", "o", "n", "a", "e"].includes(
        strList[strList.length - 1]  //判断是否是特殊拼音
      );
      if (pinYin.length >= 2 && !temp) { //如果是特殊拼音
        if (item === pinYin) return true; //如果拼音字典的key等于当前拼音，返回true
      } else {
        let result = re.test(item);   //如果不是特殊拼音，返回正则匹配结果
        return result;
      }
    })
    .sort();
  cn_list_str.value =
    keys.length > 1 ? keys.reduce((a, b) => a + dict[b], "") : dict[keys[0]]; //如果拼音列表大于1，拼接拼音列表，否则直接赋值
console.log('cn_list_str.value-->>',cn_list_str.value);
  if (!cn_list_str.value || (!cn_list_str.value.length && type === "del")) { //如果拼音列表不存在或者拼音列表长度为0并且type为del
    cn_input.value = cn_input.value.replace(
      new RegExp(`(.*)${key}`),
      `$1'${key}`
    );
  }

  if (!cn_list_str.value && !Array.isArray(cn_list_str.value)) {
    cn_list_str.value = [];
  }

  keys = cn_input.value.split("'");

  if (keys.length >= 2) {
    cn_list_str.value = [];
    findDoubleSpell(); 
  }
};

const findDoubleSpell = () => {
  let keys = cn_input.value.split("'");
  const partDict = doubleSpell[cn_input.value.charAt(0)];
  if (!partDict) return;
  if (["an"].includes(keys[keys.length - 1])) {
    let tempStr = keys[keys.length - 2];
    if (["n"].includes(tempStr.charAt(tempStr.length - 1))) {
      let newTempStr = tempStr.slice(0, tempStr.length - 1);
      keys = [
        newTempStr,
        tempStr.slice(tempStr.length - 1) + keys[keys.length - 1],
      ];
      cn_input.value = keys.join("'");
    }
  }

  let keyResult = Object.keys(partDict).filter((key) => {
    const keys = key.split("'");
    const cn_inputList = cn_input.value.split("'");
    const isLen = cn_inputList.length === keys.length;
    if (!isLen) return;
    let result = true;
    for (let i = 0; i < keys.length; i++) {
      if (keys[i].charAt(0) != cn_inputList[i].charAt(0)) {
        result = false;
      } else {
        const keyItemList = keys[i].split("");
        const strList = cn_inputList[i].split("");
        for (let j = 0; j < strList.length; j++) {
          if (!strList[j]) break;
          if (keyItemList[j] != strList[j]) {
            result = false;
          }
        }
      }
    }
    if (result && isLen) {
      return partDict[key];
    }
  });

  let strList = [];
  let singleDictList = [];
  const singleDict = dict;
  for (let key of keyResult) {
    let keyList = key.split("'");
    strList.push(partDict[key].split(","));
    for (let item of keyList) {
      if (singleDict[item]) {
        singleDictList.push(singleDict[item].split(""));
      }
    }
  }
  if (!keyResult.length && keys.length) {
    for (let item of keys) {
      if (singleDict[item]) {
        singleDictList.push(singleDict[item].split(""));
      }
    }
  }

  singleDictList = Array.from(new Set(singleDictList.flat(2)));
  strList = strList
    .flat(2)
    //@ts-ignore
    .sort((a, b) => {
      if (b.length > a.length) return -1;
    })
    .reverse();
  //@ts-ignore
  cn_list_str.value = strList.concat(singleDictList);
};

//点击数字
const clickNumber = (e, key) => {
  if (!AllKey.number.find((k) => k === key) && def_mode.value != "biaodian") {
    return;
  }

  if (input !== document.activeElement) return;
  e.preventDefault();
  e.target.style.background = "#d0d0d0";
  if (mode.value === "cn" && cn_input.value !== "") {
    let value = cut_cn_list.value[parseInt(key) - 1];
    if (!value) return;
    selectCN(value);
    setInputValue(value);
  } else {
    setInputValue(key);
  }
  emits("clickNumber", key);
};
//选择中文
const selectCN = (text) => {
  console.log("selectCN");
  let strList = cn_input.value.split("'");
  if (strList.length === 1 || text.length >= strList.length) {
    cn_input.value = "";
    cn_list_str.value = [];
    l_min.value = 0;
    l_max.value = props.maxQuantify;
    return;
  }
  setCn_input(text);
};
//设置中文输入
const setCn_input = (text) => {
  const singleDict = dict;
  let itemList = [];
  for (let key in singleDict) {
    let value = singleDict[key];
    if (typeof value === "string") {
      let valueList = value.split("");
      let item = valueList.find((item) => item === text);
      if (item) {
        itemList.push(key);
      }
    }
  }
  let str = "";
  const cn_inputList = cn_input.value.split("'");
  for (let i = 0; i < cn_inputList.length; i++) {
    let item = cn_inputList[i];
    for (let key of itemList) {
      let list = key.split("");
      let count = 0;
      for (let k = 0; k < list.length; k++) {
        if (key.charAt(k) === item.charAt(k)) {
          count += 1;
          if (
            k === 0 &&
            item.length != key.length &&
            key.length > item.length
          ) {
            // 用于处理首单词缩写法
            str = item;
          }
        }
        if (count === list.length) {
          str = item;
        }
      }
    }
  }
  cn_input.value = cn_inputList.filter((item) => item != str).join("'");
  findChinese();
};

//点击中文
const clickCN = (e, text) => {
  e.preventDefault();
  e.stopImmediatePropagation();
  showDiction.value = false;
  setInputValue(text);
  selectCN(text);

  emits("clickCN", text);
};

//cap值改变
const cap_change = () => {
  if (mainMode.value === "noCn") {
    mode.value = mode.value === "en_cap" ? "password" : "en_cap";
    return;
  }
  mode.value = mode.value === "en_cap" ? "en" : "en_cap";
};

//cn值改变
const cn_change = (params) => {
  if (mainMode.value === "noCn") {
    mode.value = params === "en" ? "cn" : "en";
  } else {
    mode.value = params;
  }
  cn_input.value = "";
  cn_list_str.value = [];
};

//返回按键
const Fanhui = () => {
  if (showDiction.value) {
    showDiction.value = false;
    return;
  }

  if (["num", "biaodian"].includes(old_mode.value) || !old_mode.value) {
    mode.value = "cn";
    return;
  }

  mode.value = old_mode.value;
};

//上一步
const previous_page = (e) => {
  e.preventDefault();
  e.stopImmediatePropagation();
  if (l_min.value > 0) {
    l_min.value = l_min.value - props.maxQuantify;
    l_max.value = l_max.value - props.maxQuantify;
  }
};

//下一步
const next_page = (e) => {
  e.preventDefault();
  e.stopImmediatePropagation();
  if (cn_list_str.value.length > l_max.value) {
    l_min.value += props.maxQuantify;
    l_max.value += props.maxQuantify;
  }
};

//删除
const del = (e) => {
  console.log('del');
  e.stopImmediatePropagation();
  e.preventDefault();
  if (input !== document.activeElement) return;
  if (mode.value === "cn" && cn_input.value !== "" && !showDiction.value) {
    cn_input.value = delStringLast(cn_input.value, cn_input.value.length);

    if (cn_input.value === "") {
      cn_list_str.value = [];
      return;
    }
    const specialPinYin = ["u", "v", "i"].includes(cn_input.value.split("")[0]);
    if (cn_list_str.value && cn_list_str.value.length === 1 && specialPinYin) {
      //@ts-ignore
      cn_list_str.value = [cn_input.value];
      return;
    }
    let re = new RegExp(`^${cn_input.value}\\w*`);
    let keys = Object.keys(dict)
      .filter((key) => {
        const result = re.test(key);
        return result;
      })
      .sort();

    cn_list_str.value =
      (keys.length > 1
        ? keys.reduce((a, b) => a + dict[b], "")
        : dict[keys[0]]) || "".split("");

    findChinese("del");
    emits("del", JSON.parse(JSON.stringify(cn_input.value)));
  } else {
    setInputValue(null, "del");
  }
};
onMounted(() => {
  inputBindKeyboard();
});
//监听输入事件
watch(
  () => props.inputEvent, 
  (val) => {
    const showKeyboard = props.showKeyboard;
    let showValue = false;

    if (val && val.target && showKeyboard) {
      input = val.target;
      def_mode.value = val.mode || "cn";
      showValue = true;
      getCaretPosition(val.target);
    }
    show.value = showValue;
  }
);
</script>
<style lang="scss" scoped>
@import './index.scss'
</style>
