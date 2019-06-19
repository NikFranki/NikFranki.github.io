---
layout: post
title: "jQuery 初始化"
date: 2019-06-05
tag: jQuery
---

# jQuery初始化

<code>jQuery</code> 风靡多时，在前端历史有着举足轻重的影响，<code>jQuery</code>本着<code>write less，do more</code>，把广大开发者带进了新的世界，<b>选择器、<code>dom</code>操作、<code>css</code>处理、事件、<code>ajax</code>、动画队列</b>是<code>jQuery</code>最为出彩的部分。前端如今变幻莫测，<code>react</code>、<code>vue</code>、<code>angular</code>三驾马车并趋前行、大行其道，回想当年，<code>jQuery</code>有过之而不过及，每个历史时期都会出现新的框架。作为一个前端开发者，框架的出现帮我们更好地完成工作，现今使用的框架迟早有一天会被淘汰，但是有个东西却不会淘汰，那就是这些框架留下的<b>思想</b>，这就要求我们做到知其然更知其所以然。这也是为什么要学习<code>jQuery</code>源码的原因了。

## jquery 是如何初始化的？

最基本的jquery初始化代码

```js
(function(global, factory) {
    'use strict';
    // 支持node.js环境
    if (typeof module === 'object' && typeof module.exports === 'object') {
        module.exports = global.document
            ? factory(global, true)
            : function(w) {
                  if (!w.document) {
                      throw new Error(
                          'jQuery requires a window with a document'
                      );
                  }
                  return factory(w);
              };
    } else {
        // 普通浏览器环境
        factory(global);
    }

    // Pass this if window is not defined yet
})(typeof window !== 'undefined' ? window : this, function(window, noGlobal) {
    ('use strict');

    var version = '3.4.1';

    // 声明jQuery构造函数，接收selector(选择器)，context(上下文)参数
    var jQuery = function(selector, context) {
        return new jQuery.fn.init(selector, context);
    };

    // jQuery上的fn方法，构成了jQuery里导出到全局可用属性和方法（整个jQuery对象，非常重要）
    jQuery.fn = jQuery.prototype = {
        // The current version of jQuery being used
        jquery: version,

        constructor: jQuery,

        // The default length of a jQuery object is 0
        length: 0
    };

    // Initialize a jQuery object
    // jQuery对象初始化方法
    // jQuery不是通过new jQuery()创建对象而是new jQuery.fn.init(selector, context)
    var init = (jQuery.fn.init = function(selector, context, root) {
        return this;
    });

    // init
    // init方法指向jQuery的原型
    init.prototype = jQuery.fn;

    if (!noGlobal) {
        window.jQuery = window.$ = jQuery;
    }

    return jQuery;
});

```

<code>jQuery</code>的初始化很有意思，并不是像大多数框架、工具一样直接在调用的时候直接调用<code>new</code>去创建一个实例，而是通过内部的<code>init</code>方法，使用类似工厂的概念去实例化一个<code>jQuery</code>对象，从而使的导出来的jQuery赋予<code>jQuery.fn</code>上的所有属性和方法，很巧妙。

## 结语

这是<code>jQuery</code>源码学习的起始篇，往后会深入探讨<code>jQuery</code>其他优秀思想，未完待续！