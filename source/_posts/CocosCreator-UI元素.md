---
title: CocosCreator-UI元素
date: 2018-05-05 23:21:16
tags:
---
![cocos_creator](/assets/cocos_creator.jpg)

Widget: 定位适应组件，看不见摸不着,可以帮助我们固定某个组件离父元素的位置

UI组件有好多好多，这里暂时用到这么多，其它的实践使用了再写

+ ProgressBar:进度条

  会有一个子组件bar
  主要设置progress的大小(0~1)，就可以设置进度

<!-- more -->
+ ScrollView: 滚动容器
+ ScrollBar: 滚动条

ScrollView 和 scrollBar 一般都是一起使用的
scrollview 中会有一个content节点，需要滚动的内容就放到这个节点中，动态这设置这个节点的高度，就可以行成滚动了

ScrollBar: 需要一个子节点作为滚动条

+ Layout: 布局盒子

layout有3种布局方式，
1、横向布局
2、纵向布局  竖起排列
3、grid布局

+ EditBox

输入框，可以多行输入，可以单行输入。
输入框可以触发几个事件
```js
    editBox.on('editing-did-ended', this.callback, this) // 输入结束时触发
    editBox.on('text-changed', (e) => {
        // 每次输入改变时触发
    }),
    callback: function (event) {
        //这里的 event 是一个 EventCustom 对象，你可以通过 event.detail 获取 EditBox 组件
        var editbox = event.detail
        this._jiname = editbox.string.trim()
        editbox.string = this._jiname
    },
```