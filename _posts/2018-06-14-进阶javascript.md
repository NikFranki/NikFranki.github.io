---
layout:     post
title:      "进阶javascript之我见"
subtitle:   ""
date:       2018-06-14
author:     "franki"
header-img: "images/post-bg-js-module.jpg"
tags:
    - 前端开发
    - JavaScript
---

* 目录  
{:toc #markdown-toc}

前端在变，框架在变，但有个东西永远都不会过时，那就是掌握了javascript，才能更好掌控未来

## 用原生js实现bind方法

返回一个永远指向传入对象的函数

```js
Function.prototype.bind = function(context) {
    var self = this;
    return function() {
        return self.apply(context, arguments);
    }
};
```

## 用原生实现继承的方法

```js
var a = {a: 1};
var b = Object.create(a);

Object.create = function(o) {
    var Fun = function() {};
    Fun.prototype = o;
    return new Fun();
}
```
