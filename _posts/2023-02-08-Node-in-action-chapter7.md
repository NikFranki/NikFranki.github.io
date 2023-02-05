---
layout:     post
title:      "Node.js 实战笔记"
date:       2022-02-08
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - Node.js
    - BackEnd
---

# Connect 自带的中间件

## 解析 cookie、请求主题和查询字符串的中间件

Node 没有解析 cookie、缓存请求体、解析复杂查询字符串之类高层 Web 程序概念的核心模块，所以 Connect 为你提供了实现这些功能的中间件

### cookieParser(): 解析 HTTP cookie

下例中的秘钥是tobi is a cool ferret:

```js
var connect = require('connect');
var app = connect()
  .use(connect.cookieParser('tobi is a cool ferret'))
  .use(function(req, res) {
    console.log(req.cookies);
    console.log(req.signedCookies);
    res.end('hello\n');
  }).listen(3000);
```

### bodyParser(): 解析请求主体

可以解析表单 POST 请求

```js
var app = connect()
  .use(connect.bodyParser())
  .use(function(req, res) {
    res.end('Registered new user: ', req.body.username);
  })
```

### limit(): 请求主体的限制

开发人员需要正确分类可接受的请求，并在恰当的时机加以限制

### query(): 查询字符串解析

解析 GET 请求

## 实现 Web 程序核心功能的中间件

### logger(): 记录请求

```js
var connect = require('connect');

var app = connect()
  .use(connect.logger())
  .use(hello)
  .listen(3000);
```

### favicon(): 提供 favicon

默认返回 Connect 的 favicon

### methodOverride(): 伪造 HTTP 方法

methodOverride() 修改了原始的 req.method 属性，但 Connect 复制了原始方法，随时都可以访问 req.originalMethod

### vhost(): 虚拟主机

vhost() 中间件是一种通过请求头 Host 路由请求的简单、轻量的方法。

### session(): 会话管理

提供强健、直观、由社区支持的会话管理

## 处理 Web 程序安全的中间件

### basicAuth(): HTTP 基本认证

可以用来给小型或者个人程序添加一个简便快捷的认证

### csrf(): 扩展请求伪造

利用站点对浏览器的信任漏洞进行攻击，经过你的程序认证的用户访问者创建或攻陷站点时，会在用户不知情的情况下代表用户向你的程序发起请求，从而实施攻击

### errorHandler(): 开发错误处理

一般用在开发环境中，不应该出现在生产环境

## 提供静态文件服务的中间件

### static(): 静态文件服务

支持 HTTP 缓存机制、范围请求

### comppress(): 压缩静态文件

压缩出站数据

### directory(): 目录列表

可以提供目录列表的小型中间件，可以用它浏览远程文件

## 具体代码细节

[chapter7](https://github.com/NikFranki/node-in-action/tree/master/chapter7)
