---
layout:     post
title:      "Node.js 实战笔记"
date:       2023-02-13
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - Node.js
    - BackEnd
---

# 部署 Node 程序并维持正常运行时间

## 安装 Node 程序

- 专用服务器
  物理服务器
- 虚拟私有服务器
  VPS（Virtual Private Server）运行在物理服务器上，得到物理服务器上一部分 RAM、处理能力和磁盘空间
- 通用云服务器
  云主机，管理自动化，通过一个远程接口进行API创建、启动、停滞和销毁

## 部署的基本知识

- 从 git 存储库部署
- 让 Node 保持运行
  Forever 可以做到
- 让正常运行时间和性能达到最优
  upstart 维护正常运行时间
- 集群 API 利用多核的优势
  主进程和工人运行在各自的操作系统进程内
- 静态文件及处理
  使用 Nginx 做 Node.js 的代理并提供文件服务的配置文件
