<template>
  <div class="wrapper">
    <div v-show="start" class="homeBtn" @click="change">
      <img style="width: 100%; height: 100%" src="@/assets/change.png" alt=""></div>
    <div class="navTitle" v-html="titleTemplate" v-if="start">
      <!--Da<span style="font-weight: 900;color: blue">:</span>/yM@r/<span style="font-weight: 900;color: red">{{flag}}</span>down-->
    </div>
    <div id="tempNav" v-if="start === false" :class="{toTop: titleReturn}"></div>
    <div v-show="start" class="editorWrapper" :class="{turnLeft: isTurnLeft, turnRight: isTurnRight}">
      <div class="editorInput">
        <!--<div class="numTag">
          <div v-for="(item, index) in rowList" :key="index">
            {{ item }}
          </div>
        </div>-->
        <textarea v-model="markdownInput" placeholder="写点什么吧" class="editor" name="editor" id cols="30" :rows="rows"></textarea>
      </div>
      <div class="editorMarkdown">
        <div class="markdownPad" v-html="mdInput" v-highlight></div>
      </div>
    </div>
  </div>
</template>

<script>
import marked from "marked";
import hljs from "highlight.js";
import "highlight.js/styles/rainbow.css";
import Vue from "vue";

Vue.directive("highlight", function(el) {
  let blocks = el.querySelectorAll("pre code");
  blocks.forEach(block => {
    hljs.highlightBlock(block);
  });
});

export default {
  mounted() {
    console.log(document.getElementsByClassName("editorWrapper")[0], "外高度");
    let editorBox = document.getElementsByClassName("editorWrapper")[0];
    this.rows = Math.floor((editorBox.clientHeight - 34) / 24);
    for (let i = 1; i <= 1; i++) {
      this.rowList.push(i);
    }
    this.typing()
  },
  computed: {
    day() {
      return this.dateInfo[2] < 10 ? "0" + this.dateInfo[2] : this.dateInfo[2];
    },
    mdInput: {
      get() {
        return marked(this.markdownInput)
      },
    }
  }, 
  data() {
    return {
      str: 'Da:/yM@r/<down',
      timer: '',
      len: 0,
      flag: "<",
      start: false,
      titleReturn: false,
      rows: 1,
      rowList: [],
      isTurnLeft: false,
      isTurnRight: false,
      currenrPage: 'edit',
      markdownInput: '',
      titleTemplate: ''
    };
  },
  methods: {
    change() {
      if (this.currenrPage === 'edit') {
        this.isTurnLeft = true
        this.isTurnRight = false
        this.currenrPage = 'markdown'
      } else {
        this.isTurnLeft = false
        this.isTurnRight = true
        this.currenrPage = 'edit'
      }
    },
    typing() {
      if (this.len <= this.str.length) {
        document.getElementById('tempNav').innerText = this.str.slice(0, this.len++) + '_';
        if (this.len !== 3 && this.len !== 4 && this.len !== 7 && this.len !== 9 && this.len !== 10) {
          this.timer = setTimeout(this.typing, 160);
        } else {
          this.timer = setTimeout(this.typing, 400);
        }
      } else {
        document.getElementById('tempNav').innerText = this.str; //结束打字,移除 _ 光标
        setTimeout(() => {
          this.titleReturn = true
        },200)
        setTimeout(() => {
          this.start = true
          this.titleTemplate = `Da<span style="font-weight: 900;color: blue">:</span>/yM@r/<span style="font-weight: 900;color: red"><</span>down`
        },600)
        clearTimeout(this.timer);
      }
    }
  },
};
</script>

<style scoped>
.homeBtn {
  width: 4rem;
  height: 4rem;
  border-radius: 1rem;
  position: absolute;
  background: rgba(0, 0, 0, 0.5);
  bottom: 3rem;
  right: 3rem;
  z-index: 10;
}
.wrapper {
  width: 100%;
  height: 100%;
  position: relative;
  overflow: hidden;
}
.editorWrapper {
  width: 100%;
  height: calc(100% - 4rem);
  /*overflow-x: hidden;
  overflow-y: auto;*/
  position: relative;
  margin-top: 4rem;
}
.editorInput {
  width: 100%;
  height: 100%;
  display: flex;
}
.editorMarkdown {
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
  transform: translateX(100%);
  padding: 0 2.4rem;
  padding-bottom: 3rem;
  overflow-y: auto;
}
.numTag {
  min-width: 2em;
  min-height: calc(100% - 3.2rem);
  /*background: #ddd;*/
  padding: 0rem 0.6rem;
  margin: 1.6rem 0;
  border-right: 0.1rem solid #ddd;
}
.numTag > div {
  line-height: 2rem;
  font-size: 1.6rem;
  text-align: left;
}
.editor {
  width: 100%;
  overflow: auto;
  word-break: break-all;
  outline: none;
  padding: 1.4rem;
  font-size: 1.6rem;
  line-height: 2rem;
}
#tempNav {
  color: #000;
  letter-spacing: -0.1rem;
  width: 100%;
  height: 4rem;
  font-size: 3rem;
  font-weight: 900;
  line-height: 4rem;
  text-align: center;
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  z-index: 11;
}
.navTitle {
  /*background: #466394; */
  background: #fff;
  color: #000;
  letter-spacing: -0.1rem;
  width: 100%;
  height: 4rem;
  font-size: 3rem;
  font-weight: 900;
  line-height: 4rem;
  text-align: center;
  position: absolute;
  z-index: 11;
}
.turnLeft {
  animation: slideLeft 0.5s;
  animation-fill-mode: forwards;
}
.turnRight {
  animation: slideRight 0.5s;
  animation-fill-mode: forwards;
}
.toTop {
  animation: toTop 0.5s;
  animation-fill-mode: forwards;
}
/* 页面向左滑动的动画 */
@keyframes slideLeft {
  100% {
    transform: translateX(-100%);
  }
}
/* 页面向右滑动的动画 */
@keyframes slideRight {
  0% {
    transform: translateX(-100%);
  }
  100% {
    transform: translateX(0);
  }
}
@keyframes toTop {
  0% {
    top: 50%;
    transform: translateY(-50%);
  }
  100% {
    top: 0;
    transform: translateY(0);
  }
}

.markdownPad >>> img {
  width: 90%;
}
.markdownPad >>> blockquote {
  border-left: #eee solid 5px;
  padding-left: 15px;
  color: #8e8e8e;
  margin-inline-start: 0px;
  margin-inline-end: 0px;
}
.markdownPad >>> ul li {
  line-height: 25px;
}
.markdownPad >>> pre code {
  color: #ccc;
  background: #2d2d2d;
  font-family: "Microsoft YaHei" !important;
}
.markdownPad >>> p code {
  color: #d34b62;
  background: #2d2d2d;
  margin: 0 2px;
  font-family: "Microsoft YaHei" !important;
}
.markdownPad >>> .squeezed-body {
  animation: squeezeBody 0.5s ease;
  -webkit-animation: squeezeBody 0.5s ease;
  width: calc(100% - 300px);
}

.markdownPad >>> .full-body {
  animation: stretchBody 0.5s ease;
  -webkit-animation: stretchBody 0.5s ease;
  width: 100%;
}

.markdownPad >>> h1,
h2,
h3,
h4,
h5,
h6 {
  font-family: "Monospace";
  font-weight: bold;
  /*margin: 3rem 0;*/
}

.markdownPad >>> h1 {
  border-bottom: 1px solid #ddd;
}

.markdownPad >>> .serif {
  font-family: "Monospace";
}

.markdownPad >>> .top-bar {
  height: 45px;
  min-height: 45px;
  position: absolute;
  top: 0;
  right: 0;
  left: 0;
}

.markdownPad >>> .bars-lnk {
  color: #fff;
}

.markdownPad >>> .bars-lnk i {
  display: inline-block;
  margin-left: 10px;
  margin-top: 7px;
}

.markdownPad >>> .bars-lnk img {
  display: inline-block;
  margin-left: 10px;
  margin-top: -15px;
  margin-right: 15px;
  height: 35px;
}

.markdownPad >>> .lateral-menu {
  background-color: #333;
  color: rgb(144, 144, 144);
  width: 300px;
  font-family: "Open Sans", "Myriad Pro", "Lucida Grande", "Lucida Sans Unicode",
    "Lucida Sans", Geneva, Verdana, sans-serif;
}

.markdownPad >>> .lateral-menu label {
  color: rgb(144, 144, 144);
}

.markdownPad >>> .lateral-menu-content {
  padding-left: 10px;
  height: 100%;
  font-size: 12px;
  font-style: normal;
  font-variant: normal;
  font-weight: bold;
  line-height: 16px;
}

.markdownPad >>> .lateral-menu-content .title {
  padding-top: 15px;
  font-size: 2em;
  height: 45px;
}

.markdownPad >>> .lateral-menu-content-inner {
  overflow-y: auto;
  height: 100%;
  padding-top: 10px;
  padding-bottom: 50px;
  padding-right: 10px;
  font-size: 0.9em;
}

.markdownPad >>> .container {
  display: flex;
  flex-direction: row;
  flex-wrap: nowrap;
  justify-content: center;
  align-items: stretch;
  width: 100%;
  height: 100%;
  padding-top: 65px;
}

.markdownPad >>> .container > * {
  display: block;
  width: 50%;
  margin-left: 10px;
  margin-right: 10px;
  max-height: 100%;
}

.markdownPad >>> .container textarea {
  resize: none;
  font-family: Consolas, "Liberation Mono", Courier, monospace;
  height: 97%;
  max-height: 97%;
  width: 45%;
}

.markdownPad >>> #preview {
  height: 97%;
  max-height: 97%;
  border: 1px solid #eee;
  overflow-y: scroll;
  width: 55%;
  padding: 10px;
}

.markdownPad >>> pre {
  white-space: pre-wrap;
  /* css-3 */
  white-space: -moz-pre-wrap;
  /* Mozilla, since 1999 */
  white-space: -pre-wrap;
  /* Opera 4-6 */
  white-space: -o-pre-wrap;
  /* Opera 7 */
  word-wrap: break-word;
  /* Internet Explorer 5.5+ */
  background-color: #f8f8f8;
  border: 1px solid #dfdfdf;
  margin-top: 1.5em;
  margin-bottom: 1.5em;
  padding: 0.125rem 0.3125rem 0.0625rem;
}

.markdownPad >>> th {
  border: 1px solid #333;
  padding: 0 1rem;
}

.markdownPad >>> td {
  border: 1px solid #333;
  padding: 0 1rem;
}

.markdownPad >>> pre {
  padding: 16px;
  overflow: auto;
  font-size: 85%;
  line-height: 1.45;
  background-color: #2d2d2d;
  border-radius: 6px;
}

.markdownPad >>> .modal-wrapper {
  position: absolute;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  z-index: 999;
  background-color: rgba(51, 51, 51, 0.5);
}

.markdownPad >>> .modal-inner {
  margin-top: 200px;
  margin-left: auto;
  margin-right: auto;
  width: 600px;
  height: 225px;
  background-color: #fff;
  opacity: 1;
  z-index: 1000;
}

.markdownPad >>> .modal-close-btn {
  float: right;
  display: inline-block;
  margin-right: 5px;
  color: #ff4336;
}

.markdownPad >>> .modal-close-btn:hover {
  float: right;
  display: inline-block;
  margin-right: 5px;
  color: #8d0002;
}

.markdownPad >>> .modal-topbar {
  clear: both;
  height: 25px;
}

.markdownPad >>> .modal-inner .link-area {
  margin: 10px;
  height: 170px;
}

.markdownPad >>> .modal-inner textarea {
  width: 100%;
  height: 100%;
  margin: 0;
  padding: 0;
}

.markdownPad >>> .version {
  color: white;
  font-size: 0.8em !important;
}

.fileInput {
  width: 5rem;
  height: 5rem;
}
</style>