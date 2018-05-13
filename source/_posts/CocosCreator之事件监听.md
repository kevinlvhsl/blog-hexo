---
title: CocosCreator之事件监听
date: 2018-05-13 12:47:35
tags: [技术, cocos, 事件监听]
---
![cocos_creator](/assets/cocos_creator.jpg)

### 事件监听
> 在cocosCreator中，是以组件的方式开发的，事件也是基于组件来监听和触发
事件处理是在节点（cc.Node）中完成的。对于组件，可以通过访问节点 this.node 来注册和监听事件。监听事件可以 通过 this.node.on() 函数来注册，
<!-- more -->
+ 早期的版本 中是通过EventManager来实现的。
添加一个事件监听的复合对象，然后绑定到第二个参数上
```
listenInputControl () {
  const listener = {
    event: cc.EventListener.TOUCH_ONE_BY_ONE,
    onTouchBegan(touchs, event) {
      const target = event.getCurrentTarget()
      cc.log(touchs.getLocation())
      // const locationInNode = target.converToNodeSpace(touchs.getLocation())
      // cc.log(locationInNode)
    },
    onTouchMoved(touchs, event) {},
    onTouchEnd(touchs, event) {},
  }
  cc.eventManager.addListener(listener, this.node)
},
```

+ 现在方式

```
this.node.on(cc.Node.EventType.TOUCH_START,function(event) {
    const tar = event.getCurrentTarget()
    for (var k in event) {   // 打印事件对象上的方法
      if (event[k] instanceof Object) cc.log('key:', event)
    }
    const deltX = event.getLocationX()
    cc.log('点击的 x, y:', event.getLocation())
}.bind(this), this.node)
```
> 值得一提的是，事件监听函数 on 可以传第三个参数 target，用于绑定响应函数的调用者。以下两种调用方式， 效果上是相同的：
```
// 使用函数绑定
this.node.on('mousedown', function ( event ) {
  this.enabled = false;
}.bind(this));

// 使用第三个参数
this.node.on('mousedown', function (event) {
  this.enabled = false;
}, this);
```
> 同一个组件中，可以自定义事件，然后在当前组件中触发 也可以在子组件中派发
```
  onLoad: function () {
    this.node.on('say-hello', function (event) {
      console.log(event.detail.msg);
    });
  },

  start: function () {
    this.node.emit('say-hello', {
      msg: '这里是当前组件触发自己监听的事件。这里是传递的数据',
    });
  },
```

+ 绑定自定义事件 Event.EventCustom
> 自定义事件是由子组件向上触发的，用于父子组件间的通信

> 自定义事件跟普通的事件有所区别， 那就是不能获取设备或者屏幕相关的参数属性，参数需要自定义传过来
```
this.node.on('gameover', function (event) {   // event CustomEvent 对象
  var data = event.detail || event.getUserData()  // 两个都是获取传过来的用户数据
  cc.log('用户数据:', event.detail, data)
  cc.audioEngine.pause(bgAudioiD)     // 在游戏结束的时候，暂停背景音乐
  event.stopPropagation()
})
```
> 触发自定义事件 也叫 *派送事件*

```
const e = new cc.Event.EventCustom('gameover', true)
e.setUserData({ a: 100, score: 444 })
this.node.dispatchEvent(e)
```


+ 子元素中监听父元素的事件
> 上面提到父元素可以监听子元素的事件，那么怎么样在子元素中监听父元素的事件呢？？
重点就是：*获取到父元素。然后添加监听事件*
```
listenTouchEvent () {
  const self = this
  // 父元素被点击，子元素做出响应
  this.parentCanvas.on(cc.Node.EventType.TOUCH_START,function(event){
    // 每次向上走140px
    const goUp = cc.moveBy(0.2, cc.p(0, 140))
    self.node.runAction(goUp)
    return true
  })
},
```


### 关闭监听

> 当我们不再关心某个事件时，我们可以使用 off 方法关闭对应的监听事件。需要注意的是，off 方法的 参数必须和 on 方法的参数一一对应，才能完成关闭。
```
cc.Class({
  extends: cc.Component,
  _sayHello: function () {
    console.log('Hello World');
  },

  onEnable: function () {
    this.node.on('foobar', this._sayHello, this);
  },

  onDisable: function () {
    this.node.off('foobar', this._sayHello, this);
  },
})
```