---
layout:     post
title:      "Node.js 实战笔记 - chapter2"
date:       2023-02-03
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - Node.js
    - BackEnd
---

# 构建有多个房间的聊天室程序

构建一个在线聊天室，用户可以在一个简单的表单中输入消息，相互聊天，消息输入后会发送给同一个聊天室内的其他用户

## 程序概览

- 输入消息
- 修改昵称
- 修改房间
- 换到新房间的结果

## 程序需求及初始设置

- 提供静态文件
- 在服务器上处理与聊天相关的消息
- 在用户的浏览器中处理与聊天相关的消息

### 提供 HTTP 和 Websocket 服务

Node 用一个端口就可以轻松地提供 HTTP 和 Websocket 两种服务

### 创建程序的文件结构

- lib
- public
  - javascript
  - stylesheets

### 指明依赖项

包描述文件

```json
{
  "name": "chatrooms",
  "version": "0.0.1",
  "description": "Minimalist multiroom chat server",
  "dependencies": {
    "socket.io": "~0.9.6",
    "mime": "~1.2.7"
  }
}
```

### 安装依赖项

在控制台上输入

```bash
npm install
```

## 提供 HTML、CSS、客户端 JavaScript 的服务器

### 创建文件静态服务器

创建静态服务器既要用到 Node 内置的功能，也要用第三方的 mime 附加模块俩确定文件的 MIME 类型

### 添加 HTML 和 CSS 文件

添加 HTML 文件

添加 CSS 文件

创建 HTTP 服务器时，需要给 CreateServer 传入一个匿名函数作为回调函数，由它来处理每个 HTTP 请求。这个回调函数接受两个参数，一个 req，一个是 res

## 用 Socket.IO 处理与聊天相关的消息

处理浏览器与服务器之间的通信，现代浏览器通过 WebSocket 进行两者的通信

### 设置 Socket.IO 服务器

### 处理程序场景及事件

- 分配昵称
- 房间变换请求
- 昵称变换请求
- 发送聊天信息
- 房间创建
- 用户断开连接

## 在程序的界面上使用客户端 JavaScript

### 将消息和昵称/房间变更请求传给服务器

处理聊天命令、发送消息、请求变更消息或昵称

### 在用户界面中显示消息及可用房间

显示文本数据

## 具体代码细节

[chapter2](https://github.com/NikFranki/node-in-action/tree/master/chapter2)
