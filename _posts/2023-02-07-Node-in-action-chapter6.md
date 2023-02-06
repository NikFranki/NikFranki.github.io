---
layout:     post
title:      "Node.js 实战笔记"
date:       2023-02-07
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - Node.js
    - BackEnd
---

# Connect

Connect 是一个框架，它使用被称为中间件的模块化组件，以及重用的方式实现 Web 程序中的逻辑。在 Connect 中，中间件组件是一个函数，它拦截 HTTP 服务器提供的请求和响应对象，执行逻辑，然后结束响应，或者把它传递给下个中间件。Connect用分派器把中间件连接在一起

提供了一个常用的组件：请求日志、静态文件服务、请求体解析、会话管理等

## 搭建一个 Connect 程序

```bash
npm install connect
```

最小的 Connect 程序

```js
var connect = require('connect');
var app = connect();
app.listen(3000);
```

## Connect 的工作机制

在 Connect 中，中间件组件通常是一个 JavaScript 函数，按惯例会接收三个参数：一个请求对象、一个响应对象、还有一个通常命名为 next 的参数，它是一个回调函数，表明这个组件已经完成了它的工作，可以执行下一个中间件

## 为什么中间件的顺序很重要

Connect 允许你定义中间件的执行顺序

### 中间件什么时候不调用 next()

如果先调用的中间件没有执行 next()，后续的中间件将不会再被执行

### 用中间件的顺序执行认证

需要做认证时，几乎所有的程序都会被认证

## 挂载中间件和服务器

Connect 中有一个挂载的概念，这是一个简单而强大的组织工具，可以给中间件或整个程序定义一个路径前缀

```js
var connect = require('connect');

connect.use('/admin', restrict).listen(3000);
```

## 创建可配置中间件

### 认证中间件

Basic 认证是一种简单的认证机制，借助带着 Base64 编码认证信息的 HTTP 请求头中的 authorization 字段进行认证

### 显示管理面板和中间件

中间件 admin 在请求 URL 上用 switch 语句做了一个原始的路由器

## 使用错误处理中间件

用函数返回另一个函数（闭包）

### Connect 的默认错误处理器

### 自行处理程序错误

### 使用多个错误处理中间件组件

## 具体代码细节

[chapter6](https://github.com/NikFranki/node-in-action/tree/master/chapter6)
