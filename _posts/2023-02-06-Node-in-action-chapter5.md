---
layout:     post
title:      "Node.js 实战笔记 - chapter5"
date:       2023-02-06
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - Node.js
    - BackEnd
---

# 存储 Node 程序中的数据

> 本章内容 <br>
内存和文件系统数据存储<br>
传统的关系型数据库存储<br>
非关系型数据库存储<br>

## 无服务器型的数据存储

从系统的管理的角度来看，最方便的存储机制是那些不用维护 DBMS 的存储，比如内存存储和基于文件的存储

### 内存存储

特点是读取和写入的速度都很快，缺点是服务器和程序重启后数据就丢失了

理想的用途是存放少量经常使用的数据。用来追踪记录最近一次重启服务器后页面访问次数的计数器就是这样的应用场景

比如下面这段代码

```js
var http = require('http');
var counter = 0;

var server = http.createServer(function(req, res) {
  counter++;
  res.write('I have been accessed ' + counter + 'times.');
  res.end();
});

server.listern(8000);
```

### 基于文件的存储

基于文件的存储，用文件系统存放数据。开发人员经常用这种存储方式来存储程序的配置信息，但你也可以用它来做数据的持久化保存，这些数据在程序和服务器重启后依旧有效

问题：当多个开发人员对于同一个文件进行读写后，可能会造成冲突，导致某个开发人员的配置被覆盖

## 关系型数据库管理系统

关系型数据库（RDBMS）可以存储复杂的信息，并且查询起来很容易，应用场景主要是内容管理、客户关系管理和购物车

开源的数据库有 MySQL 和 PostgreSQL，这是两个最流行的全功能关系型数据库

### MySQL

MySQL 是最流行的 SQL 数据库，Node 社区对它的支持很好

### PostgreSQL

PostgreSQL 因其与标准的兼容性和健壮性收到认可，很多开发人员对它的喜爱程度超过了其他的 RDBMS

## NoSQL 数据库

在数据库世界刚具雏形之时，非关系型数据库才是标准。流行的 NoSQL 数据库有：Redis 和 MongoDB

### Redis

Redis 非常适合处理那些不需要长期访问的简单数据存储，比如短信和游戏中的数据。Redis 把数据存在 RAM 中，并在磁盘中记录数据的变化。这样做的缺点是它的存储空间有限，但好处是数据操作非常快，即使 Redis 服务器崩溃，RAM 中的内容丢了，可以用磁盘中的日志恢复数据

- 连接 Redis 数据库
- 操作 Redis 数据库
- 用哈希表存储和获取数据
- 用链表存储和获取数据
- 用集合存储和获取数据
- 用信道传递数据

### MongoDB

MongoDB 是一个通用的非关系型数据库，使用 RDBMS 的那类程序都可以使用 MongoDB

- 连接 MongoDB
- 访问 MongoDB 集合
- 将文档插入到集合中
- 用文档 ID 更新数据
- 搜索文档
- 删除文档

### Mongoose

Mongoose 是 LearnBoost 提供的一个 Node 模块，让你可以顺畅地使用 MongoDB

## 具体代码细节

[chapter5](https://github.com/NikFranki/node-in-action/tree/master/chapter5)
