---
title: vue 自定义指令防止重复提交
date: 2020-03-30 15:18:57
tags: [技术, vue]
---

### 问题
在开发过程中，经常会提交表单。但如果后端没有做控制，前端也没有做控制的情况下，常常会有重复提交的问题。

### 解决办法

+ 1、手动写一个标识，
```js
function submit() {
  if (this.lock) return
  this.lock = true; // lock 非vue可以写在外面
  doSomeThing();
  var t = setTimeout(() => {
    clearTimeout(t);
    lock = false; // 释放
  }, 2000); // 时间可以根据实际情况定
}
```

+ 2、不用每个方法去写，利用标签的属性去处理（一般表单都是由按钮去触发）

```js
Vue.directive('fast-click', {
  inserted(el, binding) {
    el.addEventListener('click', () => {
      el.setAttribute('disabled', 'disabled');
      el.disabled = true;
      let t = setTimeout(() => {
        clearTimeout(t);
        t = null;
        el.disabled = false;
        el.removeAttribute('disabled');
      }, 1500); // 时间可以根据实际情况定
    })
  }
});
```
- 使用
```html
<button @click="submit" v-fast-click>提交</button>
<el-button @click="submit" v-fast-click>提交</el-button>
<i-button @click="submit" v-fast-click>提交</i-button>
```
