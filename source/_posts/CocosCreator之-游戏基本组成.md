---
title: CocosCreator之-游戏基本组成
date: 2018-05-05 22:54:41
tags: [技术, cocos, 小游戏]
---

![cocos_creator](/assets/cocos_creator.jpg)

### cocos creator开发游戏
> 开发一个简单的游戏，都有哪些组成部分呢？
+ 1、场景
游戏主要是由场景组成的，不同的场景转表示不能的展示。  场景中都有一个根元素，Canvas。 一般开发的时候，场景的大小就是canvas的大小，也就是用户可以看到的范围，会将一些图片、文字资源都放置到这个canvas下面。排在上面的显示在前面。

+ 2、脚本
JavaScript脚本主要是来控制游戏逻辑的。
<!-- more -->
+ 3、图片及音视频、其他资源文件，可以是预置文件等。
场景文件、脚本文件、资源文件都放置assets目录下，打包以后，这个里面的文件内容大小，将直接影响最终打包的大小。 很有可能就超过了微信小程序设置的4M大小限制， 这里可以去看之前提到的包大小解决方案。

### 游戏打包后

> 这里主要讲一下构建发布成微信小游戏后的文件目录
打包的文件夹是由构建时指定的。 我们一般会生成在当工程项目下的build文件下下。
构建后生成一个wechatgame的目录（如果发布其他平台，就生成并列的目录）。这个目录下都有哪些文件呢？
src     // 项目的project.js和settings.js 可能还会有其他文件
res     // 项目资源文件目录（图片、音频、视频等），这个文件夹一般最大
  --import  // 图片资源引用的json信息存储
  --raw-assets  // 开发中引用到的源图片音视频文件
  --raw-internal  // 内置的图片文件
libs    // 引用的包等
  --weapp-adaper  // 微信小游戏适配器
  --xmldom  // 一些dom解析包
  --wx-downloader.js  // cocoscreator自带的download工具
cocos2d-js-min.js     // cocos引擎文件
game.js   // 游戏入口文件
game.json // 游戏配置文件
main.js   // 游戏引擎生成的入口
project.config.json // 项目配置文件



