---
title: 折腾linux经历
date: 2017-04-11 14:48:48
tags: [技术, 服务器, linux]
---

## 在linux下安装Nodejs
![](/assets/flower.jpg)
+ 第一步，首先到[nodejs官网](https://nodejs.org/en/download/)下载[linux版本](https://nodejs.org/dist/v6.10.2/node-v6.10.2-linux-x64.tar.xz)的nodejs包

+ 第二步，下载好以后，通过xftp工具传到服务器上， 通过命令 `xz node-v6.10.2-linux-x64.tar.xz  和 tar -xvf node-v6.10.2-linux-x64.tar`

+ 第三步，将nodejs链接到全局 `ln -s /解压缩后存放目录/node-v6.10.2-linux-x64/bin/node /usr/local/bin/node     ln -s /解压缩后存放目录/node-v6.10.2-linux-x64/bin/npm /usr/local/bin/npm`

+ 第四步，导出到环境变量 `export NODE_HOME=/usr/local/node/node-v6.10.2-linux-x64/bin`  `export PATH=$NODE_HOME:$PATH`

最后就能愉快的在全局使用node命令和npm命令了


> 还有其他安装的方法
sudo apt-get install nodejs
sudo apt-get install npm
或者 通过下载源码，然后用gcc 编译工具编译
参考：[http://www.cnblogs.com/8765h/p/4777746.html](http://www.cnblogs.com/8765h/p/4777746.html)
