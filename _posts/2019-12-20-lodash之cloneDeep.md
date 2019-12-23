---
layout:     post
title:      "lodash之cloneDeep"
subtitle:   ""
date:       2019-12-20
author:     "franki"
header-img: "images/post-bg-js-module.jpg"
tags:
    - 前端开发
    - 源码分析
    - lodash
---

* 目录  
{:toc #markdown-toc}

> 好的实现总是让人向往

工作中经常使用lodash，一直有这样的感受‘怎么可以这么好用’，一个简单的调用就可以完成复杂的操作，令自己产生想要深入了解lodash，尤其是lodash深拷贝部分的知识。

下面是本人这几天整理的源码笔记，文笔有限，可能出现错字、语句不顺等状态，还请谅解。

### cloneDeep 调用

首先，在lodash中要进行深拷贝是如何使用的？

```js
// ./cloneDeep.js
const CLONE_DEEP_FLAG = 1
const CLONE_SYMBOLS_FLAG = 4

function cloneDeep(value) {
  return baseClone(value, CLONE_DEEP_FLAG | SLONE_SYMBOLS_FLAG);
}

// 假设调用的情形是
const objects = [{a: 1}, {b: 2}]
const deep = _.cloneDeep(objects)
```

你一定很好奇，cloneDeep的第二个参数到底是啥玩意？平常开发中几乎没有用到！这个疑问下面会进行详解。

本人一直践行"有图有真相"，附上 lodash cloneDeep 相关的 flow chat（流程图）

![cloneDeep](/images/posts/lodash/lodash cloneDeep flow chat.png)

接着是 baseClone 的 flow chat（流程图）

![baseClone](/images/posts/lodash/lodash baseClone flow chat.png)

是不是有点吓住了？不要害怕，只要跟着流程图这条基线往下读，一定收获满满，剩下的只是进入相关的方法进行仔细斟酌的过程而已。

回到上面的 cloneDeep.js `CLONE_DEEP_FLAG | SLONE_SYMBOLS_FLAG` 这里面是在搞什么东西？

我们在日常工作中最经常用的莫过于 `|| && !`

其实上面的就是一个位运算

下面进行说明

```js
var a = 1 转为 0001

var b = 4 转为 0100

var mask = a | b 0101 5

var hasA = a & mask 0001 1

var hasB = a & mask 0100 4
```

其实就是位掩码的相关操作，其中掩码中的每个选项的值都等于2的幂，相关直接使用变量，有点是可以节省内存。

基本操作如下：

* a \| b 添加标志位a 和 b
* mask & a 取出标志位 a
* mask & ~a 清除标志位 a
* mask ^ a 取出与 a 不同的部分

第二个参数主要的作用是告知baseClone是否要进行深拷贝和是否要赋值Symobl类型

### baseClone 调用

```js
function baseClone(value, bitmask, customizer, key, object, stack) {
  let result
  const isDeep = bitmask & CLONE_DEEP_FLAG // 深拷贝 true
  const isFlat = bitmask & CLONE_FLAT_FLAG // 拷贝原型链 false
  const isFull = bitmask & CLONE_SYMBOLS_FLAG // 拷贝symbol true

  if (customizer) {
    result = object ? customizer(value, key, object, stack) : customizer(value)
  }
  if (result !== undefined) {
    return result
  }
  if (!isObject(value)) {
    return value
  }
  ...
}
```

上面代码相关参数说明如下：

* value 要被克隆的值
* bitmask 掩码
* customizer 自定义克隆函数
* key value对应的key值
* object value 的父对象
* stack 栈 （解决循环引用）

```js
function baseClone(value, bitmask, customizer, key, object, stack) {
  ...
  const isArr = Array.isArray(value)
  const tag = getTag(value)
  if (isArr) {
    result = initCloneArray(value)
    if (!isDeep) {
      return copyArray(value, result)
    }
  }
  ...
}
```

initCloneArray 克隆数组

进入一探究竟

```js
function initCloneArray(array) {
  const { length } = array
  const result = new array.constructor(length) // 这里相当于new Array(length) 不知道为何要用constructor

  // Add properties assigned by `RegExp#exec`.
  // 这里相当于判断reg.exec(str) 或者 str.match(reg)
  if (length && typeof array[0] === 'string' && hasOwnProperty.call(array, 'index')) {
    result.index = array.index
    result.input = array.input
  }
  return result
}
```

返回一个分配了相同内存长度的数组

与之相似的是 initCloneObject 克隆对象

```js
function initCloneObject(object) {
  return (typeof object.constructor === 'function' && !isPrototype(object))
    ? Object.create(Object.getPrototypeOf(object))
    : {}
}

```

其中isPrototype是判断是否是原型，

Object.create(Object.getPrototypeOf(object)) 让新创建的对象与object有相同的原型

其他的cloneBuffer、initCloneByTag可以自行阅读，这里不做讲解。

### 重头戏

如何拷贝，保证不可变？

```js
const keysFunc = isFull
    ? (isFlat ? getAllKeysIn : getAllKeys)
    : (isFlat ? keysIn : keys)

const props = isArr ? undefined : keysFunc(value)
arrayEach(props || value, (subValue, key) => {
  if (props) {
    key = subValue
    subValue = value[key]
  }
  // Recursively populate clone (susceptible to call stack limits).
  assignValue(result, key, baseClone(subValue, bitmask, customizer, key, value, stack))
})
return result
```

由上文代码得知，keysFunc的结果是getAllKeys

props的结果取决于isArr

arrayEach对于数组的每一项进行处理

assign 对于结果进行对象的赋值，里面涉及到递归操作

整个数组的深拷贝过程就完成了。

对象的深拷贝是如何的呢？

对象的深拷贝类似于以上数组的拷贝，只是少了一开始是数组的那一步。

目的都是通过分配新的变量，分配内存空间，接着通过递归操作重新赋值达到深拷贝的要求。

主要是讲对象和数组的深拷贝，其他的深拷贝需要各位大牛自行探索!

写完收工，最后一句，码字不易，摸摸头发，还在!
