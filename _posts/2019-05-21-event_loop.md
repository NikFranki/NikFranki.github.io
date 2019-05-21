---
layout: post
title: "图解event loop"
date: 2019-05-21
tag: browser
---

# event loop
众所周知，javascript是单线程的，代码只能一步一步执行，如果一个任务消耗的时间太长，那么后面的任务就会被阻塞，js就是通过event loop(事件循环)来解决这个问题的。

## stack queue heap event loop
- stack 吃多了上吐
- queue 吃多了下泄
- heap 存放对象



## 具体表现
![](/images/posts/browser/event_loop.png)

### 有何用处
对于js的执行顺序了解更加深入，规范js代码书写。