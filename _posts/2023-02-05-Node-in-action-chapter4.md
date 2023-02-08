---
layout:     post
title:      "Node.js 实战笔记 - chapter4"
date:       2023-02-05
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - Node.js
    - BackEnd
---

# 构建 Node Web 程序

本章将会介绍 Node 为创建 HTTP 服务器所提供的工具

## HTTP 服务器的基础知识

### Node 如何向开发者呈现 HTTP 请求

Node 的 http 模块提供 HTTP 服务器和客户端接口

```js
var http = require('http');

http.createServer(function(req, res) {
  // 处理请求
});
```

### 一个用 “Hello World” 做响应的 HTTP 服务器

调用 res.write() 方法，将响应的数据写到 socket 中，然后 res.end() 方法结束这个响应

```js
var http = require('http');

http.createServer(function(req, res) {
  // res.write('Hello World!');
  // res.end();

  // 可以简写为
  res.end('Hello World');
});
```

### 读取请求头及设定响应头

Node 提供几个修改响应头的方法: res.setHeader(field, value) res.getHeader(field) res.removeHeader(field)

```js
var body = 'Hello World';
res.setHeader('Content-Length', body.length);
res.setHeader('Content-Type', 'text/plain');
res.end(body);
```

### 设定 HTTP 响应的状态码

```js
res.statusCode = 302;
```

## 构建 RESTful Web 服务

- POST 向待办事项提供事项
- GET 显示当前事项列表，或者显示某一事项详情
- DELETE 从待办事项中移除事项
- PUT 修改已有事项

## 提供静态文件服务

- 创建一个静态服务器
- 处理服务器错误
- 用 fs.stat() 实现先发制人的错误处理

## 从表单中接受用户输入

- 处理提交的表单域
  表单提交的 Content-Type 通常为两种：application/x-www-form-urlencoded 默认值 和 multipart/form-data 有文件或二进制数据时使用
- 用 formidable 处理上传的文件
- 计算上传进度

## 用 HTTPS 加强程序的安全性

## 具体代码细节

[chapter4](https://github.com/NikFranki/node-in-action/tree/master/chapter4)
