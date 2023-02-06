---
layout:     post
title:      "Node.js 实战笔记"
date:       2023-02-09
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - Node.js
    - BackEnd
---

# Photos

完成一个 Photos 应用，支持图片上传、下载、浏览功能

实现：

## 使用 `express-generator` 生成项目骨架

找到合适的目录，控制台输入以下命令

```bash
express-generator -e photo
```

## 渲染视图

- 调整视图的查找
- 配置默认的模板引擎
- 启用视图缓存，减少文件 IO

## 列表路由

文件放在 routes 目录下

定义大致如下：

```bash
router.get('/upload', function(req, res) {
  res.render('photos/upload', {
    title: 'Photo upload'
  });
});
```

## 处理表单和文件上传

- 定义照片模型

| 注意：前提是需要安装好 mongodb，把相应的数据库资源准好，启动好

定义如下：

```bash
const mongoose = require("mongoose");

mongoose.set('strictQuery', false);

mongoose.connect("mongodb://127.0.0.1/photo_app");

const schema = new mongoose.Schema({
  name: String,
  path: String,
  image: {
    data: Buffer,
    contentType: String
  }
});

// Compile model from schema
module.exports = mongoose.model("Photo", schema);
```

- 创建照片上传表单
  
  略

- 显示照片列表

  略

## 创建资源下载

- 创建图片下载路径

构建 img 标签外面添加一个指向GET /photo/:id/download 路由的链接

- 实现照片下载路由

触发 GET /photo/:id/download，调用 express 的下载方法

具体有两种：

  1 res.sendfile() 传输数据，浏览器会解释数据
  
  2 res.download(path, filename) 触发浏览器下载

## 小结

学会如何从头开始创建 Express 文件，以及处理常见的 web 开发任务，学会如何组织目录，如何使用环境变量

## 具体代码细节

[chapter8](https://github.com/NikFranki/node-in-action/tree/master/chapter8)
