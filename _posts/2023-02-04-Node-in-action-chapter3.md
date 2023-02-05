---
layout:     post
title:      "Node.js 实战笔记"
date:       2022-02-04
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - Node.js
    - BackEnd
---

# Node 编程基础

Node 不像大多数开源平台那样，它很容易设置，对内存和硬盘的空间没有过多的要求；也不需要复杂的集成开发环境来构建系统；但是需要面对两大难题

- 如何组织代码
- 如何做到异步变成

## Node 功能的组织及重用

Node 模块允许你从被引入的文件中选择要暴露给程序的函数和变量；如果模块返回的函数或变量不止一个，那它可以通过设定 exports 对象的属性来指明它们

### 创建模块

模块既可能是一个文件，也可能是包含一个或者多个文件的目录

举个例子

a.js

```js
exports.add = function() {
  return 1 + 1;
};
```

b.js

```js
var a = require('a.js');
a.add();
```

### 用 module.exports 微调模块的创建

返回一个构建函数来代替返回一个对象

### 用 node_modules 重用模块

利于与他人共享模块

### 注意事项

- 如果模块是目录，在模块目录中定义模块必须被命令成 index.js，除非你在 package.json 里面指明 main 对应的文件名称
- Node 把模块对象缓存起来

## 异步编程技术

事件发生会触发响应逻辑，在 Node 的世界里流行两种响应逻辑管理方式：回调和事件监听

### 用回调处理一次性事件

回调是一个函数，被当做参数传递给异步函数，描述了异步操作完成之后要做什么

### 用事件发射器处理重复性事件

事件发射器会触发事件，并且在那些事件被触发时能处理它们；如 HTTP 服务器、TCP 服务器和流，都被当成事件发射器

### 异步开发的难题

创建异步程序时，你必须密切关注程序的执行流程，并瞪大眼镜盯着程序的状态：事件轮训的条件、程序变量，以及其他随着程序逻辑执行而发生变化的资源

## 异步逻辑顺序化

在异步程序执行过程中，有些任务会随时发生，跟程序中的其他部分在做什么没有关系，什么时候做这些任务都不会出问题；但也有些任务只能在特定任务做了之后做；

### 什么时候使用串行流程控制

可以使用回调让几个异步任务按顺序执行，如果任务很多，必须组织一下

### 实现串行化流程控制

需要先把这些任务按预期的执行顺序放入到一个数组中，这个数组起到队列的作用，完成一个任务后按顺序从数组取出下一个

### 实现并行化流程控制

为了让异步任务并行执行，仍然是把任务放到数组中，但任务的存放顺序无关紧要；每个任务都应该调用处理器函数增加已完成任务的计数值，当所有任务完成后，处理器函数应该执行后续的逻辑

### 利用社区里的工具

流行的比如 Nimble、Step 和 Seq

## 具体代码细节

[chapter3](https://github.com/NikFranki/node-in-action/tree/master/chapter3)