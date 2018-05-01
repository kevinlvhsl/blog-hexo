---
title: nginx 初识
date: 2018-02-12 10:51:34
tags: [nginx, 服务器]
---
![nginx](/assets/nginx.png)
## 牛逼的web服务器工具:nginx

> 一直与服务器打交道，也很多次遇到nginx，一直没有去认真学习过nginx
  昨天同事遇到了nginx配置的问题，折腾了一下午一直没有解决，最后也是以一种妥协的方式搞定的，
  为了以后遇到这样的问题，能够更快更好的搞定，决定要认真的研究一下 神秘的nginx。

### 什么是 Nginx？
Nginx 最初是作为一个 Web 服务器创建的，用于解决 C10k 的问题。作为一个 Web 服务器，它可以以惊人的速度为您的数据服务。但 Nginx 不仅仅是一个 Web 服务器，你还可以将其用作反向代理，与较慢的上游服务器（如：Unicorn 或 Puma）轻松集成。你可以适当地分配流量（负载均衡器）、流媒体、动态调整图像大小、缓存内容等等。
基本的 nginx 体系结构由 master 进程和其 worker 进程组成。master 读取配置文件，并维护 worker 进程，而 worker 则会对请求进行实际处理。
<!-- more -->
#### 一、安装（我的mac电脑）、启动、停止
+ 安装
> 其实安装到时很容易，安装的方式也有很多种。 我采用了mac上最简单的方式 homebrew。 先来到 /etc 目录下
 `brew install nginx`

安装完以后，可以在终端输出的信息里看到一些配置路径：
```
/usr/local/etc/nginx/nginx.conf （配置文件路径）
/usr/local/var/www （服务器默认路径）
/usr/local/Cellar/nginx/1.8.0 （安装路径）
```

+ 启动
在终端中输入

ps -ef|grep nginx
如果执行的结果是
  501 15800     1   0 12:17上午 ??         0:00.00 nginx: master process /usr/local/Cellar/nginx/1.8.0/bin/nginx -c /usr/local/etc/nginx/nginx.conf
  501 15801 15800   0 12:17上午 ??         0:00.00 nginx: worker process
  501 15848 15716   0 12:21上午 ttys000    0:00.00 grep nginx
表示已启动成功，如果不是上图结果，在终端中执行
/usr/local/Cellar/nginx/1.8.0/bin/nginx -c /usr/local/etc/nginx/nginx.conf
命令即可启动nginx。这时候如果成功访问localhost:8080，说明成功安装和启动好了。
+ 停止
在终端中输入 ps -ef|grep nginx  获取到nginx的进程号，注意是找到“nginx:master”的那个进程号，如下面的进程好是 15800
```
501 24547     1   0 10:44上午 ??         0:00.00 nginx: master process nginx
  501 24548 24547   0 10:44上午 ??         0:00.00 nginx: worker process
  501 27590 24634   0  1:53下午 ttys028    0:00.00 grep --color=auto --exclude-dir=.bzr --exclude-dir=CVS --exclude-dir=.git --exclude-dir=.hg --exclude-dir=.svn nginx
```
在终端中输入以下几种命令都可以停止

`kill -QUIT  24547` (从容的停止，即不会立刻停止)
`Kill -TERM  24547` （立刻停止）
`Kill -INT  24547`  （和上面一样，也是立刻停止）
+ 重启
如果配置文件错误，则将启动失败，所以在启动nginx之前，需要先验证在配置文件的正确性，如下表示配置文件正确
$ /usr/local/Cellar/nginx/1.8.0/bin/nginx -t -c /usr/local/etc/nginx/nginx.conf
nginx: the configuration file /usr/local/etc/nginx/nginx.conf syntax is ok
nginx: configuration file /usr/local/etc/nginx/nginx.conf test is successful

重启有两种方法
1）在终端输入输入如下命令即可重启

$ cd /usr/local/Cellar/nginx/1.8.0/bin/
$ ./nginx -s reload
$ nginx -s reload
2）根据进程号重启，执行命令 kill -HUP 进程号

#### 二、基本命令
要启动 nginx，只需输入：
`[sudo] nginx`

当你的 nginx 实例运行时，你可以通过发送相应的信号来管理它：

`[sudo] nginx -s signal`
可用的信号：
+ `stop` – 快速关闭
+ `quit` – 优雅关闭 (等待 worker 线程完成处理)
+ `reload` – 重载配置文件
+ `reopen` – 重新打开日志文件

#### 三、查看配置
> 要想熟悉nginx，就必须对它的配置文件 了熟于心。
nginx 的配置文件，默认的位置包括：

`/etc/nginx/nginx.conf`,
`/usr/local/etc/nginx/nginx.conf`,
或 `/usr/local/nginx/conf/nginx.conf`

> 配置文件由厦门的部分构成
##### 1、上下文
– 分块，你可以声明指令 – 类似于编程语言中的作用域
```
worker_processes 2; # 全局上下文指令

http {              # http 上下文
  gzip on;         # http 上下文中的指令
  server {          # server 上下文
    listen 80;      # server 上下文中的指令
  }
}
```
##### 2、 指令  – 可选项，包含名称和参数，以分号结尾. 也就是配置项
> 指令类型： 普通指令、数组指令、 行动指令、处理请求、server_name指令、listen指令、root,location,try_files指令

+ 普通指令: 在每个上下文仅有唯一值。而且，它只能在当前上下文中定义一次。子级上下文可以覆盖父级中的值，并且这个覆盖值只在当前的子级上下文中有效。 (不能在同一个上下文中指定同一普通指令2次)
+ 数组指令 在统一上下文中添加多条指令， 将添加多个值， 而不是完全覆盖。 在子集上下文中定义指令将覆盖给父级上下文中的值。
```
error_log /var/log/nginx/error.log;
error_log /var/log/nginx/error_notive.log notice;
error_log /var/log/nginx/error_debug.log debug;
server {
  location /downloads {
    # 下面的配置会覆盖父级上下文中的指令
    error_log /var/log/nginx/error_downloads.log;
  }
}
```
+ 行动指令 ： 改变事情的指令。 根据模块的需要，它继承的行为会有所不同。
例如 rewrite指令， 只要匹配的都会执行。 return 返回
```
server {
  rewirte ^ /foobar;
  localtion /foobar {
    rewrite ^ /foo;
    rewrite ^ /bar;
  }
}
```
如果用户想尝试获取 /sample：
server的rewrite将会执行，从 /sample rewrite 到 /foobar
location /foobar 会被匹配
location的第一个rewrite执行，从/foobar rewrite到/foo
location的第二个rewrite执行，从/foo rewrite到/bar
```
server {
  location / {
    return 200;
    return 404;
  }
}
```
在上述的情况下，立即返回200。

+ 处理请求 在nginx内部，你可以指定多个虚拟服务器，每个虚拟服务器用server{}上小文描述。
```
server {
  listen      *:80 default_server;
  server_name netguru.co;
  return 200 "Hello from netguru.co";
}
server {
  listen      *:80;
  server_name foo.co;
  return 200 "Hello from foo.co";
}
server {
  listen      *:81;
  server_name bar.co;
  return 200 "Hello from bar.co";
}
```
这将告诉nginx 如何处理到来的请求。nginx 将会首先通过检查listen 指令来测试哪一个虚拟主机在监听给定的IP端口组合。
然后server_name 指令的值将检测host头（存储着主机域名）
nginx 将会按照下列顺序选择虚拟主机：
  1、匹配server_name 指令的IP-端口主机
  2、拥有default_server标记的IP-端口主机
  3、首先定义的IP-端口主机
  4、如果没有匹配，拒绝连接。
例如下面的例子：
```
Request to foo.co:80     => "Hello from foo.co"
Request to www.foo.co:80 => "Hello from netguru.co"   // 没有匹配到servername 匹配到了端口
Request to bar.co:80     => "Hello from netguru.co"   // 没有匹配到servername 匹配到了端口
Request to bar.co:81     => "Hello from bar.co"
Request to foo.co:81     => "Hello from bar.co"   // 没有匹配到servername 匹配到了端口
```

+ server_name 指令  接受多个值。 它还处理通配符匹配和正则表达式
```
server_name netguru.co www.netguru.co; # exact match
server_name *.netguru.co;              # wildcard matching
server_name netguru.*;                 # wildcard matching
server_name  ~^[0-9]*.netguru.co$;     # regexp matching
```
当有歧义时，nginx 将使用下面的命令：

  1、确切的名字

  2、最长的通配符名称以星号开始，例如“* .example.org”。

  3、最长的通配符名称以星号结尾，例如“mail.**”

  4、首先匹配正则表达式（按照配置文件中的顺序）

nginx会存储3个hash哈希表： 1:确切的名字 2:以星号※开始的通配符  3:以星号结尾的通配符

如果 结果不在任何表中，则将按照顺序进行正则表达式测试。

值得注意的是  `server_name .netguru.co;`   是一个来自下面的缩写 `server_name netguru.co  www.netguru.co *.netguru.co;`
有一点不同， .netguru.co 存储在第二张表，意味着它比显示申明的慢一点。

+ linsten 指令  接受 IP:端口值,   还可以指定 UNIX-domain 套接字 / 甚至可以使用主机名
```
listen 127.0.0.1:80;
listen 127.0.0.1;    # by default port :80 is used
listen *:81;
listen 81;           # by default all ips are used
listen [::]:80;      # IPv6 addresses
listen [::1];        # IPv6 addresses

listen unix:/var/run/nginx.sock;    // 套接字
listen localhost:80;   //甚至可以使用主机名
listen netguru.co:80;
```
-- 主机名和套接字请慎用， 由于主机可能无法启动nginx，导致无法绑定在特定的tcp socket
最后，如果指令不存在，则使用 *:80。

+ root, location, 和 try_files 指令
root 指令 设置请求的根目录，允许 nginx 将传入请求映射到文件系统。
```
server {
  listen 80;
  server_name netguru.co;
  root /var/www/netguru.co;
}
根据给定的请求，指定 nginx 服务器允许的内容
netguru.co:80/index.html     # returns /var/www/netguru.co/index.html
netguru.co:80/foo/index.html # returns /var/www/netguru.co/foo/index.html
```
+ location 指令  根据请求的 URI 来设置配置。
location [modifier] path
location /foo/ {
  # ...
}
如果没有指定修饰符，则路径被视为前缀，其后可以跟随任何东西。
/foo
/fooo
/foo123
/foo/bar/index.html
...
此外，在给定的上下文中可以使用多个 location 指令。

*Nginx 也提供了一些修饰符，可用于连接 location。这些修饰符将影响 location 模块使用的地方，因为每个修饰符都分配了优先级。
```
=           - Exact match
^~          - Preferential match
~ && ~*     - Regex match
no modifier - Prefix match
```
Nginx 会先检查精确匹配。如果找不到，我们会找优先级最高的。如果这个匹配依然失败，正则表达式匹配将按照出现的顺序进行测试。至少，最后一个前缀匹配将被使用。
```
location /match {
  return 200 'Prefix match: matches everything that starting with /match';
}
location ~* /match[0-9] {
  return 200 'Case insensitive regex match';
}
location ~ /MATCH[0-9] {
  return 200 'Case sensitive regex match';
}
location ^~ /match0 {
  return 200 'Preferential match';
}
location = /match {
  return 200 'Exact match';
}
/match/    # => 'Exact match'
/match0    # => 'Preferential match'
/match1    # => 'Case insensitive regex match'
/MATCH1    # => 'Case sensitive regex match'
/match-abc # => 'Prefix match: matches everything that starting with /match'
```

+ try_files 指令  尝试不同的路径，找到一个路径就返回。
`try_files $uri index.html =404;`  所以对于 /foo.html 请求，它将尝试按以下顺序返回文件：
1、$uri ( /foo.html )
2、index.html
3、如果什么都没找到则返回 404
有趣的是，如果我们在服务器上下文中定义 try_files，然后定义匹配的所有请求的 location —— try_files 将不会执行。
server {
  try_files $uri /index.html =404;
  location / {
  }
}
因此，你应该避免在 server 上下文中出现 try_files:  而应该写在location里面
server {
  location / {
    try_files $uri /index.html =404;
  }
}




--- 本文参考 [linux学习](https://mp.weixin.qq.com/s/mdpzDKXVM_NBGxTp2YYR-w)