---
layout:     post
title:      "Node.js 实战笔记"
date:       2022-02-14
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - Node.js
    - BackEnd
---

# 超越 Web 服务器

## Socket.IO

- 创建一个最小的 Socket.IO 程序
- 用 Socket.IO 触发页面和 CSS 重载
- Socket.IO 的其他用法
  展示上传进度给到浏览器用户

## 深入 TCP/IP 网络

- 处理缓冲区和二进制数据
  文本数据与二进制数据
- 创建 TCP 服务器
  写数据
  读取数据
  用 socket.pipe(socket) 连接两个流
  处理不干净的断开
  全拼在一起

- 创建 TCP 客户端

## 跟操作系统交互的工具

- 单例的全局 process 对象
  用 process.env 获取和设定环境变量
  监听 process 发出的特殊事件，比如 exit 和 uncaughtException
  监听 process 发出的单例事件，比如 SIGUSR2 和 SIGKILL
- 使用文件系统模块
  readFile
  writeFile
  ReadStream
  WriteStream
  open
  read
  write
  close
  stat
  staSync
- 繁衍外部进程
  cp.exec() 在回调中繁衍命令并缓存结果的高层 API
  cp.spawn() 将单例命令繁衍 Child-Process 对象中的底层 API
  cp.fork() 用内置的 IPC 通道繁衍额外的 Node 进程的特殊办法

  子进程的好与坏 使用子进程既有好处，也有补足。一个明显的缺点是需要执行装在用户机器上的程序，你的应用要依赖于它。另一方面，使用外部程序让开发者可以借用由其他语言编写的丰富应用。

## 开发命令行工具

- 解析命令行工具
- 处理 stdin 和 stdout
- 添加色彩输出

## 具体代码细节

[chapter13](https://github.com/NikFranki/node-in-action/tree/master/chapter13)
