---
title: CocosCreator之本地存储
date: 2018-05-12 11:28:47
tags: [技术, cocos]
---
![cocos_creator](/assets/cocos_creator.jpg)
###  本地存储

cocos中的本地存储sys.localStorage其实也就是widow对象下的localStorage
[源代码位置](https://github.com/cocos-creator/engine/blob/master/cocos2d/core/platform/CCSys.js#L900)
<!-- more -->
```
/**
     * cc.sys.localStorage is a local storage component.
     * @property {Object} localStorage
     */
    try {
        var localStorage = sys.localStorage = win.localStorage;
        localStorage.setItem("storage", "");
        localStorage.removeItem("storage");
        localStorage = null;
    } catch (e) {
        var warn = function () {
            cc.warnID(5200);
        };
        sys.localStorage = {
            getItem : warn,
            setItem : warn,
            removeItem : warn,
            clear : warn
        };
    }
```

使用起来方式也跟window下一样，主要有4个方法
setItem: (key, value) 设置一个值
getItem: (key)        获取key对应的个值
removeItem: (key)     移除某个key对应的值
clear: ()             移除所有
`cc.sys.localStorage.setItem('name', 'kevin')`

`cc.sys.localStorage.getItem('name')`

`cc.sys.localStorage.removeItem('name')`

`cc.sys.localStorage.clear()`