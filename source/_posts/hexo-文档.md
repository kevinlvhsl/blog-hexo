---
title: hexo 基本命令
date: 2017-05-03 18:48:48
tags: 工具
---
Welcome to [Hexo](https://hexo.io/)! This is your very first post. Check [documentation](https://hexo.io/docs/) for more info. If you get any problems when using Hexo, you can find the answer in [troubleshooting](https://hexo.io/docs/troubleshooting.html) or you can ask me on [GitHub](https://github.com/hexojs/hexo/issues).

## Quick Start

### Create a new post

``` bash
$ hexo new "My New Post"
```

More info: [Writing](https://hexo.io/docs/writing.html)

### Run server

``` bash
$ hexo server
```

More info: [Server](https://hexo.io/docs/server.html)

### Generate static files

``` bash
$ hexo generate
```

More info: [Generating](https://hexo.io/docs/generating.html)
<!-- more -->
### Deploy to remote sites

``` bash
$ hexo deploy
```
### hexo命令行使用
```
hexo help #查看帮助
hexo init #初始化一个目录
hexo new "postName" #新建文章
hexo new page "pageName" #新建页面
hexo generate #生成网页，可以在 public 目录查看整个网站的文件
hexo server #本地预览，'Ctrl+C'关闭
hexo deploy #部署.deploy目录
hexo clean #清除缓存，**强烈建议每次执行命令前先清理缓存，每次部署前先删除 .deploy 文件夹**
简写
hexo n == hexo new
hexo g == hexo generate
hexo s == hexo server
hexo d == hexo deploy
```
#### 文章内容
```
 title: Hello World
date: 2015-07-30 07:56:29 #发表日期，一般不改动
categories: hexo #文章文类
tags: [hexo,github] #文章标签，多于一项时用这种格式
---
正文，使用Markdown语法书写
```
#### 每次deploy时不输入邮箱和密码

如果觉得每次部署的时候都要输入用户名和密码，可以这样解决：开始，到你的用户目录下新建一个_netrc文件。
我的操作：可以直接复制一个.gitconfig文件复件，然后改名为_netrc，注意：n前面有个下划线。
然后把_netrc文件拖入notepad++ 里，输入：
```
machine github.com
login hello
password hello123
```

More info: [Deployment](https://hexo.io/docs/deployment.html)

### 简书上几个号的hexo搭建博客教程
璀璨星空: [windows下搭建hexo博客](http://www.jianshu.com/p/e858a90d0a17)
吴小龙同學: [手把手教你建github技术博客](http://www.jianshu.com/p/701b1095da11#)
潘柏信: [HEXO+Github,搭建属于自己的博客](http://www.jianshu.com/p/465830080ea9#)

### 当前使用的主题
[yilia](https://github.com/litten/hexo-theme-yilia) 作者[Litten](http://litten.me)
