---
layout:     post
title:      "编写高质量JavaScript的基本要点"
subtitle:   ""
date:       2018-05-11
author:     "franki"
header-img: "images/post-bg-js-module.jpg"
tags:
    - 前端开发
    - JavaScript
---

* 目录  
{:toc #markdown-toc}

每个开发者常常会遇到需求三不五时地变更，对于代码的改动也是非常频繁的，这个时候代码的健壮性、可读性就异常重要了，推倒重来的做法无疑是最低效的。

![javascript](/images/posts/h5/javascript.png)

### 书写可维护的代码(writing Maintainable Code)

大家都知道，软件bug的修复是昂贵的，并且随着时间的推移，这些bug的成本会增加，当你发现它的时候立即修改它最好不过了，但是当你有其他任务忘记了此bug的修改，忘了那个特定的代码，一段时间后就要：

* 花时间学习和理解这个问题
* 花时间了解应该解决的问题代码

还有随着应用的成熟，还会有很多其他的事情发生，这要求你进行审查和修改、调整。例如：

* bug是暴露的
* 新功能被添加到应用程序
* 程序在新的环境下工作
* 代码改变用途
* 代码得完全重头开始，或移植到另一个架构上或者使用另一种语言

由此可以得出，创建可维护的代码对程序的成功至关重要。

可维护的代码应该做到：

* 可读的
* 一致的
* 可预测的
* 看上去像是同一个人写的
* 已记录

### 最小全局变量(Minimumizing Globals)

JavaScript通过函数管理作用域。在函数内声明的变量只有函数内部能访问。另一个是全局变量是在任何函数外面声明的或未声明直接简单使用的。

```js
myglobal = "hello"; // 不推荐
console.log(myglobal); // "hello"
console.log(window.myglobal); // "hello"
console.log(window["myglobal"]); // "hello"
console.log(this.myglobal); // "hello"
```

### 全局变量的问题

全局变量的问题在于，你的JavaScript应用程序和web页面上的所有代码都共享了这些全局变量，他们住在同一个全局命名空间，所以当程序的两个不同部分定义同名但不同作用的全局变量的时候，命名冲突在所难免。

隐式全局变量

```js
function sum(x, y) {
    // 不推荐写法：隐式全局变量
    result = x + y;
    return result;
}
```

此段代码中的result没有声明。代码照样运作正常，但在调用函数后你最后的结果就多一个全局命名空间，这可以是一个问题的根源。

正常做法

```js
function sum(x, y) {
    var result = x + y;
    return result;
}
```

另一个创建隐式全局变量的反例就是使用任务链进行部分var声明。

```js
function foo() {
    // 反例 勿使用
    var a = b = 0;
}
```

此现象发生的原因在于这个从右到左的赋值，首先，是赋值表达式b = 0，此情况下b是未声明的。这个表达式的返回值是0，然后这个0就分配给了通过var定义的这个局部变量a。换句话说，就好比你输入了：

```js
var a = (b = 0);
```

比较好的做法

```js
function foo() {
    var a, b;
    // a = b = 0; // 两个均是局部变量
}
```

### var 的副作用 var 声明全局变量无法删除

```js
// 定义三个全局变量
var global_var = 1;
global_novar = 2; // 反面教材
(function () {
   global_fromfunc = 3; // 反面教材
}());

// 试图删除
delete global_var; // false
delete global_novar; // true
delete global_fromfunc; // true

// 测试该删除
typeof global_var; // "number"
typeof global_novar; // "undefined"
typeof global_fromfunc; // "undefined"
```

### 单var 形式

```js
function func() {
   var a = 1,
       b = 2,
       sum = a + b,
       myobject = {},
       i,
       j;
   // function body...
}
```

### 对象的循环用for-in，并去除Object.prototype.hasOwnProperty的属性

```js
for (var i in man) {
    if (Object.prototype.hasOwnProperty.call(man, i)) {
        console.log(man[i]);
    }
}
```

### switch模式(switch Pattern)

用switch语句可以增强可读性和健壮性

```js
var inspect_me = 0,
    result = '';
switch (inspect_me) {
case 0:
   result = "zero";
   break;
case 1:
   result = "one";
   break;
default:
   result = "unknown";
}
```

### 避免(Avoiding) eval()

```js
// 反面示例
var property = "name";
alert(eval("obj." + property));

// 更好的
var property = "name";
alert(obj[property]);
```

### 花括号{}(Curly Braces)

花括号（亦称大括号，下同）应总被使用

```js
// 糟糕的实例
for (var i = 0; i < 10; i += 1)
   alert(i);
// 好的实例
for (var i = 0; i < 10; i += 1) {
   alert(i);
}
```

### 命名方式

```js
// 构造函数用大驼峰：MyConstructor()
// 普通变量或函数用小托峰：MyConstructor()
// 常量用全大写：MAX_WIDTH
// 私有变量：用下划线
var person = {
    getName: function () {
        return this._getFirst() + ' ' + this._getLast();
    },

    _getFirst: function () {
        // ...
    },
    _getLast: function () {
        // ...
    }
};
使用尾下划线表示私有，如name_和getElements_()
使用一个下划线前缀表_protected（保护）属性，两个下划线前缀表示__private （私有）属性
Firefox中一些内置的变量属性不属于该语言的技术部分，使用两个前下划线和两个后下划线表示，如：__proto__和__parent__。
```

### module 模块化

最简单直接的方式创建闭包

```js
(function(){}())
// 
(function(){})()
// 若要引用外部的全局变量，更好的方式是通过参数的形式传入
(function(){})(JQuery)

// 常见用法
var blogModule = (function() {
  var my = {}, privateName = "简书";

  function privateAddTopic(data) {
    // 这里处理内部代码
  }

  my.Name = privateName;
  my.AddTopic = function(data) {
    privateAddTopic(data);
  };
  return my;
})()
// 声明了一个全局变量blogMoudule，里面返回两个共享的变量，Name, AddTopic,而内部的变量却不可见
```

### 高级用法

扩展 (通过全部变量blogModule以参数形式参入，接着扩展AddPhoto方法，最后还是返回my对象，声明的变量还是blogModule,确保扩展方法是统一个全局变量)

```js
var blogModule = (function(my) {
  my.AddPhoto = function() {
    // 添加内部代码
  };
  return my;
})(blogModule)
```

松耦合扩展 这是确保cnblogs对象，在存在的时候直接用，不存在的时候直接赋值为{}，我们来看看如何利用这个特性来实现Module模式的任意加载顺序

```js
var blogModule = (function (my) {

    // 添加一些功能

    return my;
} (blogModule || {}));
```

紧耦合扩展 虽然松耦合扩展很牛叉了，但是可能也会存在一些限制，比如你没办法重写你的一些属性或者函数，也不能在初始化的时候就是用Module的属性。紧耦合扩展限制了加载顺序，但是提供了我们重载的机会，看如下例子：

```js
var blogModule = (function(my) {
  var oldAddPhoto = my.AddPhoto;
  // 重载
  my.AddPhoto = function() {
    // 内部代码
  };
  return my;
})(blogModule)
```

克隆与继承

```js
var blogModule = (function(old) {
  var my = {},
      key;

      for (key in old) {
        if (old.hasOwnProperty(key)) {
          my[key] = old[key];
        }
      }

      var oldAddPhotoMethod = old.AddPhoto;

      // 重写AddPhoto方法
      my.AddPhoto = function() {

      }
})(blogModule)

// 这种方式灵活是灵活，但是也需要花费灵活的代价，其实该对象的属性对象或function根本没有被复制，只是对同一个对象多了一种引用而已，所以如果老对象去改变它，那克隆以后的对象所拥有的属性或function函数也会被改变，解决这个问题，我们就得是用递归，但递归对function函数的赋值也不好用，所以我们在递归的时候eval相应的function。不管怎么样，我还是把这一个方式放在这个帖子里了，大家使用的时候注意一下就行了。
```

子模块

```js
blogModule.CommentSubModule = (function () {
    var my = {};
    // ...

    return my;
} ());
```
