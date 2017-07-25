---
title: 如何用CMD快速起一个服务？
date: 2017-07-25 07:58:45
tags: node javascript http-server
---

# 如何用CMD快速起一个服务？
** http-server ** 是一个简单的零配置命令行HTTP服务器， 基于 node.js.
如果你不想重复的写 node.js 的 web-server.js，就可以用一下方法快速启动一个 webservce .

### 安装

``` bash
$ npm install http-server 
```


### windows 使用方法

``` bash
//进入需要创建服务的文件夹
//按住shift + 鼠标右键， 选择‘在此处打开命令窗口’
$ http-server
```


这样您就启动了一个http-server 服务，默认端口号：8080

#### 您可以访问

> http://localhost:8080/ 或者 http://127.0.0.1:8080/ 

### http-server 常用参数

> //修改端口 （例如: http-server -p 8087）
>-p + 端口号 (默认 8080)    
>
>//修改ip地址    
>-a + IP 地址 (默认 0.0.0.0)     
>
> //禁止日志信息输出
>-s or --silent 
>         
>//启用 CORS via the Access-Control-Allow-Origin header
>--cors 