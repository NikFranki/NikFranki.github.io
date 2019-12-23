---
layout:     post
title:      "jQuery 数据缓存"
subtitle:   ""
date:       2019-06-07
author:     "franki"
header-img: "images/post-bg-js-module.jpg"
tags:
    - 前端开发
    - jQuery
    - 源码分析
---

* 目录  
{:toc #markdown-toc}

引入缓存的作用

* 允许我们在dom元素添加自定义的属性，避免循环引用的的内存泄漏风险
* 用于存储跟dom节点相关的数据，包括事件、动画等
* 一种低耦合的方式让dom和缓存数据能够联系起来

这里面有两个概念需要知悉

dataPriv:  一个Data实例，保存jQuery内部数据，如果jQuery的事件、动画、等数据都由这个访问器保存和获取

dataUser: 一个Data实例，保存用户通过jQuery的API存储的数据

使用方式

jQuery.data(ele, key, value) 或者 $.data(ele, key, value)

```js
var obj = {};
jQuery.data(obj, 'name', 'franki');
```

代码执行顺序为：

jQuery.data -> Data.prototype.access -> Data.prototype.set -> Data.prototype.cache -> 赋值

代码片段分析：

access 判断操作

```js
access: function(owner, key, value) {
    // 根据传入的key value 判断是进行数据读取还是数据存储
    if (
      key === undefined ||
      (key && typeof key === 'string' && value === undefined)
    ) {
      return this.get(owner, key);
    }

    this.set(owner, key, value);
  
    return value !== undefined ? value : key;
}
```

set 操作

```js
// 主要进行数据存储操作
set: function(owner, data, value) {
  var prop,
    cache = this.cache(owner);

    if (typeof data === 'string') {
      cache[camelCase(data)] = value;
    } else {
      for (prop in data) {
        cache[camelCase(prop)] = data[prop];
      }
    }
    return cache;
},
```

cache 操作

```js
// 判断当前owner是否存在缓存，若有直接返回返回缓存的数据，this.expando是判断用户设置缓存唯一的表示，保证
// 每次或者其他用户都不一样，确保数据正确性
cache: function(owner) {
  var value = owner[this.expando];
  if (!value) {
    value = {};
    if (acceptData(owner)) {
      if (owner.nodeType) {
        owner[this.expando] = value;
      } else {
        Object.defineProperty(owner, this.expando, {
          value: value,
          configurable: true
        });
      }
    }
  }

  return value;
},
```

使用方式

jQuery.data(ele, key) 或者 $.data(ele, key)

```js
var obj = {};
jQuery.data(obj, 'name');
```

代码执行顺序为：

jQuery.data -> Data.prototype.access -> Data.prototype.get

大体同上面的分析类似，只是set的操作，换成get操作

具体如下

get 操作

```js
 get: function(owner, key) {
   return key === undefined
    ? this.cache(owner)
    : // Always use camelCase key (gh-2257)
    owner[this.expando] && owner[this.expando][camelCase(key)];
 },
```

上面的调用方式是直接通过$.data() 或者是 jQuery.data()的方式去调用的，下面来介绍通过实例的方式去调用

$('body').data()

代码执行顺序为：$('div') -> .data -> access -> 执行access参数里面的回调函数 -> 返回结果

```js
jQuery.fn.extend({
  data: function(key, value) {
    var i,
      name,
      data,
      elem = this[0],
      attrs = elem && elem.attributes;

      return access(
        this,
        function(value) {
          var data;

          if (elem && value === undefined) {
            data = dataUser.get(elem, key);
            if (data !== undefined) {
              return data;
            }

            data = dataAttr(elem, key);
            if (data !== undefined) {
              return data;
            }

            return;
          }

          this.each(function() {
            dataUser.set(this, key, value);
          });
        },
        null,
        value,
        arguments.length > 1,
        null,
        true
      );
  },
}
```

上面的代码指明了数据的获取方式，首先从全局的用户缓存里面取，如果有就则返回用户缓存的值，如果没有就去dataAttr里面找,找到则返回。

dataAttr 代码分析

```js
function dataAttr(elem, key, data) {
  var name;

  // 从html5 标签 data-* 获取值
  if (data === undefined && elem.nodeType === 1) {
    name = 'data-' + key.replace(rmultiDash, '-$&').toLowerCase();
    data = elem.getAttribute(name);

    if (typeof data === 'string') {
      try {
        data = getData(data);
      } catch (e) {}

      dataUser.set(elem, key, data);
    } else {
      data = undefined;
    }
  }
  return data;
}
```

**jQuery.data() 与 .data()**  区别

* 看jQuery.data(element,[key],[value])，每一个element都会有自己的一个{key:value}对象保存着数据，所以新建的对象就算有key相同它也不会覆盖原来存在的对象key所对应的value，因为新对象保存是是在另一个{key:value}对象中
* $("div").data("a","aaaa") 它是把数据绑定每一个匹配div节点的元素上

总结：

jQuery的cache其实就围绕set get方法进行数据的读写操作，只不过根据调用方式不同，数据存储的地方不同罢了。
