---
layout:     post
title:      "细读你不知道的JavaScript中卷"
subtitle:   "发现未知"
date:       2020-02-02
author:     "franki"
header-img: "images/posts/javascript/you-dont-javascript-1.jpg"
tags:
  - 前端开发
  - javascript
---

* 目录  
{:toc #markdown-toc}

## 第一部分 类型和语法

### 第1章 类型

JavaScript 有七种内置类型

* null
* undefined
* boolean
* number
* string
* object
* symbol (es6新增)

可以使用 typeof 运算符来查看值的类型，它返回的类型的字符串值。一般来说，它们的类型与它们的数值都是一一对应的。

需要注意的是：

typeof null === 'object'

正确的结果应该是null，但是这是JavaScript的bug，但是到目前为止，都没有去修复它，因为修复它需要付出太大的代价。

### 第2章 值

#### 2.1 数组

数组可以容纳任何类型的值，可以是字符串、数字、对象、甚至是其他数组

```js
var a = [1, "2", [3]];

a.length; // 3
a[0] === 1; // true
a[2][0] === 3; // true
```

数组声明不需要声明其长度

```js
var a = [];
a[0] = 1;
a.length; // 1
```

稀疏数组

```js
var a = [];
a[0] = 1;
a[2] = 2;

a[1]; // undefined
a.length; // 3
```

数组通过数字进行索引，有趣的是它们也是对象，所以可以包含字符串键值和属性（但是不会计算在数组的长度内）

```js
var a = [];

a[0] = 1;
a['foobar'] = 2;

a.length; // 1
a['foobar']; // 2
a.foobar; // 2
```

类数组

类数组需要通过工具函数，例如indexOf(..) concat(..) forEach(..)来实现

什么情况产生类数组，一些dom查询就会返回dom元素列表，它们并非是真正意义上的数组。

```js
function transformArr() {
  var arr = Array.prototype.slice.call(arguments);
  return arr;
}

transformArr(1, 2); // [1, 2]

// es6 的Array.from(..)  更加简单
var arr = Array.from(arguments);
```

#### 2.2 字符串

字符串跟数组的很多操作类似

```js
var a = 'foo';
var b = ['f', 'o', 'o'];

a.length; // 3
b.length; // 3

a.indexOf('o'); // 1
b.indexOf('o'); // 1

var c = a.concat('bar'); // 'foobar'
var d = b.concat(['b', 'a', 'r']);

a === c; // false
b === d; // false

a; // 'foo'
b; // ['f', 'o', 'o']
```

但是这并不意味着它们是字符串数组

```js
a[1] = 'O';
b[1] = 'O';

a; // 'foo'
b; // ['f', 'O', 'o']
```

JavaScript中字符串是不可变的，而数组是可变的。

#### 2.3 数字

JavaScript中的数字常量一般用十进制表示。

```js
var a = 42;
var b = 42.3;

// 数字前面、后面的0可以忽略
var a = 0.42;
var a = .42;
var a = 42.0;
var a = 42.;
```

toFixed(..)指定小数部分的显示位数。

```js
var a = 42.59;

a.toFixed(0); // '43'
a.toFixed(1); // '42.6'
a.toFixed(2); // '42.59'
a.toFixed(3); // '42.590'
a.toFixed(4); // '42.5900'
```

无效语法

42.toFixed(3); // SyntaxError

// 这样是可以的

(42).toFixed(3); // '42.000'

0.42.toFixed(3); // '0.420'

42..toFixed(3); // '42.000'

42 .toFixed(3); // '42.000'

一个.被认为是数字的一部分，第二个.是属性访问运算符。

较小的数值

```js
0.1 + 0.2 === 0.3; // false
```

上面相加的结果比较接近的数字是0.300000000000000,所以判断条件为false；

二进制的浮点数最大问题是精度的问题。

数字安全范围

最大的整数为2^53-1,最小的整数为-(2^53-1)

#### 2.4 特殊数值

null、undefined它们即是类型也是值

null 与 undefined的区别

* null指的是空值
* undefined只没有值（未赋值）

NaN 不是数字的变量。

#### 2.5 值和引用

JavaScript对值的引用和赋值，根据值的类型来决定

```js
var a = 2;
var b = a; // b 是 a值的一个副本
b++;
a; // 2
b; // 3

var c = [1, 2, 3];
var d = c; // d是[1,2,3]的一个引用
d.push(4);
c; // [1,2,3,4]
d; // [1,2,3,4]

```

简单值总是通过值复制的方式来赋值，包括null、undefined、字符串、数字、布尔和es6的symbol。

```js
function foo(x) {
  x.push(4);
  x; // [1,2,3,4]
  
  x = [4,5,6];
  x.push(7);
  x; // [4,5,6,7]
}

var a = [1,2,3];
foo(a);

a; // [1,2,3,4]不是[4,5,6,7]
```

向函数传递a的时候，实际是将引用a的一个复本赋值给x，而a仍然指向[1,2,3]。x=[4,5,6]不影响a的指向，所以a仍然指向[1,2,3,4]。

小结：

JavaScript中的的数组通过数字索引的一组任意类型的值。字符串和数组操作类似，但是他们的行为特征不一样。

基本类型里面定义了几个特殊的值。

null类型只有一个值null，undefined类型也只有一个值undefined。void运算符返回undefined。

数字类型有几个特殊值，NaN（not a number）、+Infinity、-Infinity和-0。

基本类型值（字符串和数字等）通过值复制来赋值/传递，而复合值（对象等）通过引用复制来赋值/传递。与其他语言不通的是，JavaScript中的引用指向值。

### 第3章 原生函数

#### 3.1 内部属性

```js
var a = new String('abc');
typeof a; // 'object'并非'String'
```

typeof 返回的是对象类型的子类型。

内部的[[class]]

```js
Object.prototype.toString.call([1,2,3]); // '[object Array]'
Object.prototype.toString.call({}); // '[object Object]'
Object.prototype.toString.call(/regex-literal/i); // '[object RegExp]'
```

#### 3.2 封装对象包装

封装对象扮演十分重要的角色。基本类型没有.length和.toString()这样的属性和方法，需要通过封装对象才可以访问。此时JavaScript会自动为基本类型封装一个对象。

```js
var a = 'abc';
a.length; // 3
a.toUpperCase(); // 'ABC'
```

自行封装基本类型

```js
var a = 'abc';
var b = new String(a);
var c = Object(a);

typeof a; // 'string'
typeof b; // 'object'
typeof c; // 'object'

b instanceof String; // true
c instanceof String; // true

Object.prototype.toString.call(b); // '[object String]'
Object.prototype.toString.call(c); // '[object String]'
```

3.3 拆封

```js
var a = new String('abc');
var b = new Number(42);
var c = new Boolean(true);

a.valueOf(); // 'abc'
b.valueOf(); // 42
c.valueOf(); // true
```

#### 3.4 原生函数为构造函数

```js
var a = new Array(3);
a; // [undefined, undefined, undefined]
```

应该避免使用Object(..)、Function(..)、RegExp(..)

```js
var c = new Object();
c.foo = "bar";
c; // { foo: "bar" }

var d = { foo: "bar" };
d; // { foo: "bar" }
var e = new Function( "a", "return a * 2;" );

var f = function(a) {
  return a * 2;
}

function g(a) {
  return a * 2;
}

var h = new RegExp( "^a*b+", "g" );
var i = /^a*b+/g;
```

使用构造函数来创建对象，无法像对象字面量那样一次性设置多个属性，必须逐一设定。

小结：

JavaScript为基本数据类型提供了封装对象，称为原生函数（String、Number、Boolean等）。它们为基本数据类型提供了该子类型所有的方法和特性。

### 第4章 强制类型转换

JavaScript的强制类型转换常常被人诟病！引发社区一次又一次的争议。

#### 4.1 值类型转换

将值从一种类型转换为另一种类型通常称为类型转换，可以区分为显式类型转换和隐式类型转换。

```js
var a = 42;
var b = a + ''; // 隐式强制类型转换
var c = String(a); // 显式强制类型转换
```

#### 4.2 抽象值操作

toString

```js
var a = 42;
a.toString(); // '42'
```

普通的对象调用会直接返回'[object, Object]'

数组的默认toString()方法经过了重新定义

```js
var a = [1,2,3];
a.toString(); // '1,2,3'
```

JSON.stringfy(..) 将json对象序列化为字符串时也用到了toString。

```js
JSON.stringfy(42); // "42"
JSON.stringfy('42'); // ""42""
JSON.stringfy(null); // "null"
JSON.stringfy(true); // "true"
```

JSON.stringfy(..)在对象遇到undefined、function和symbol时会自动将其忽略，在数组中则会返回null。

```js
JSON.stringfy(undefined); // undefined
JSON.stringfy(function() {}); // undefined
JSON.stringfy([1, undefined, function() {}, 4]); // '[1, null, null, 4]'
JSON.stringfy({a: 2, b: function(){}}); // '{"a": 2}'
```

对于循环引用的对象执行JSON.stringfy()会出错。

对含有非法的JSON值的对象做字符串化，或者对象中的某些值无法被序列时，就需要定义toJSON()方法来返回一个安全的JSON值。

```js
var o = {};

var a = {
  b: 42,
  c: o,
  d: function(){}
};

// 创建一个循环引用
o.e = a;

// 循环引用在这里会产生错误
// JSON.stringfy(a);

// 自定义的JSON序列化
a.toJSON = function() {
  return {b: this.b};
};

JSON.stringfy(a); // "{"b": 42}"
```

JSON.stringfy(..)可以传递一个replacer，它可以是数组或者函数，用指定对象序列化过程中那些属性应该被处理，那些应该被排除。

```js
var a = {
  b: 42,
  c: '42',
  d: [1,2,3]
};

JSON.stringfy(a, ['b', 'c']); // "{"b": 42, "c": "42"}"

JSON.stringfy(a, function(k, v) {
  if (k !== "c") {
      return v;
  }
});
// "{"b": 42, "d": [1,2,3]}"
```

ToNumber

有时候我们需要将非数字值当做数字来使用，比如数学运算。

其中true转换为1，false转换为0.undefined转换为NaN，null转换为0。

```js
var a = {
  valueOf: function() {
    return "42";
  }
};

var b = {
  toString: function() {
    return "42";
  }
};

var c = [4, 2];
c.toString = function() {
  return this.join(""); // "42"
};

Number(a); // 42
Number(b); // 42
Number(c); // 42
Number(""); // 0
Number([]); // 0
Number(['abc']); // NaN
```

ToBoolean

假值

以下是假值

* undefined
* null
* false
* +0、-0和NaN
* ""

假值布尔强制转换结果为false

假值对象

   ```js
   var a = new Boolean(false);
   var b = new Number(0);
   var c = new String('');
   ```

   它们都是封装了假值的对象。它们的结果都是true。

真值

假值以外的值都是真值

```js
var a = 'false';
var b = '0';
var c = '""';
```

它们的结果都为真，不过""除外。

#### 4.3 显式强制类型转换

是指那些显而易见的类型转换，很多类型转换都属于此列。

类似于静态语言中的类型转换。

##### 4.3.1 字符串和数字之间的显式转换

```js
var a = 42;
var b = String(a);

var c = "3.14";
var d = Number(c);

b; // '42'
d; // 3.14
```

##### 4.3.2 显式解析数字字符串

```js
var a = '42';
var b = '42px';

Number(a); // 42
ParseInt(a); // 42

Number(b); // NaN
parseInt(b); // 42
```

解析允许字符串含有非数字字符串，解析从做到右的顺序，如果遇到非数字字符串就停下。而转换不允许出现非数字字符，否则会失败并返回NaN。

##### 4.3.3 显式转换布尔值

```js
var a = '0';
var b = [];
var c = {};

var d = '';
var e = 0;
var f = null;
var g;

Boolean(a); // true
Boolean(b); // true
Boolean(c); // true

Boolean(d); // false
Boolean(e); // false
Boolean(f); // false
Boolean(g); // false
```

显式转换为布尔值最常用的方式是!!，因为第二个!会将结果翻转会原值。

```js
var a = '0';
var b = [];
var c = {};

var d = '';
var e = 0;
var f = null;
var g;

!!a; // true
!!b; // true
!!c; // true

!!d; // false
!!e; // false
!!f; // false
!!g; // false
```

#### 4.4 隐式强制类型转换

字符串和数字之间的隐式强制类型转换

```js
var a = [1,2];
var b = [3,4];

a+b; // '1,23,4'
```

如果某个操作数是字符串的话，+操作直接拼接操作。如果一个操作数是对象（数组），则首先对其ToPrimitive抽象操作。

由于ToNumber抽象操作同样处理数组，但是数组的valueOf()操作无法得到简单的基本数据类型。于是它调用toString()。因此a转换为1,2 b转换为3,4。+ 拼接为'1,23,4'。

```js
var a = {
  valueOf: function() {
    return 42;
  },
  toString: function() {
    return 2;
  }
}

a+''; // '42'
String(a); // '2'
```

a+'' 会对a调用valueOf()方法，然后通过ToString()抽象操作将返回值转换为字符串。而String(a)则直接调用ToString()。

隐式强制类型转换为布尔值

（1）if (..)语句中的条件判断表达式

（2）for(..)语句中的条件判断表达式（第二个）

（3）while()和do while()中的条件表达式

（4）? : 中的条件判断表达式

（5）逻辑运算符||与&&左边的操作数

```js
var a = 42;
var b = 'abc';
var c = null;

a || b; // 42
a && b; // 'abc'

c || b; // 'abc'
c && b; // null
```

|| 会对第一个操作数进行条件判断，如果不是布尔值，就会先进行ToBoolean强制类型转换，然后再进行条件判断，如果条件结果为true则返回第一个操作数

&& 与 上面的过程相差无异，只有后面的，当第一个判断结果为true的是后，返回第二个操作的结果。

#### 4.5 宽松相当于严格相等

宽松相等 ==

允许在相等比较中进行强制类型转换

严格相等 ===

不允许在相等比较中进行强制类型转换

抽象比较

字符串与数字的比较

```js
var a = 42;
var b = '42';

a == b; // true
```

如果比较当中一个是字符串，会首先进行ToNumber操作，然后再进行比较

1) 如果Type(x)是字符串，则返回ToNumber(x) == y的结果

2）如果Type(y)是字符串，则返回x == ToNumber(y)的结果

其他类型与布尔类型之间的相等比较

```js
var a = '42';
var b = true;
a == b; // false
```

1) 如果Type(x)是布尔类型，则返回ToNumber(x) == y的结果

2）如果Type(y)是布尔类型，则返回x == ToNumber(y)的结果

上面的过程是，首先把b转换为1，1与'42'比较转换成1 == 42显然不等。

null和undefined之间的相等比较

1）如果x为undefined，y为null，则结果为true

2）如果x为null，y为undefined，则结果为true

```js
var a = null;
var b;

a == b; // true
a == null; // true
b == null; // true

a == false; // false
b == false; // false
a == ""; // false
b == ""; // false
a == 0; // false
b == 0; // false
```

null 与 undefined之间的强制类型转换是安全可靠的，上例中其他值无法得到假阳值。

1. 对象与非对象之间的相等比较

1）如果Type(x)是对象，则返回ToPrimitive(x) == y的结果

2）如果Type(y)是对象，则返回x == ToPrimitive(y)的结果

```js
var a = 42;
var b = [42];

a == b; // true
```

[42]首先会调用ToPrimitive抽象操作，返回‘42’，编程'42' == 42;然后又变成 42 == 42，最后二者相等。

例外

```js
var a = null;
var b = Object( a );
a == b; // 和Object()一样 // false
var c = undefined;
var d = Object( c );
c == d; // 和Object()一样 // false
var e = NaN;
var f = Object( e );
e == f; // 和new Number( e )一样 // false
```

因为没有对应的封装对象，所以 null 和 undefined 不能够被封装(boxed)，Object(null) 和 Object() 均返回一个常规对象。

NaN能够被封装为数字封装对象，但拆封之后NaN == NaN返回false，因为NaN不等于NaN

假值的相等比较

```js
"0" == null; // false
"0" == undefined; // false
"0" == false; // true -- 晕!
"0" == NaN; // false
"0" == 0; // true
"0" == ""; // false

false == null; // false
false == undefined;  // false
false == NaN; // false
false == 0; // true -- 晕!
false == ""; // true -- 晕!
false == []; // true -- 晕!
false == {}; // false

"" == null; // false
"" == undefined; // false
"" == NaN; // false
"" == 0; // true -- 晕!
"" == [];  // true -- 晕!
"" == {}; // false

0 == null; // false
0 == undefined; // false
0 == NaN; // false
0 == []; // true -- 晕!
0 == {}; // false
```

小结：

强制类型转换包含：显式和隐式

作为一个有使命感的JavaScript开发人员，很有必要深入了解强制类型转换，可以做到取其精华，去其糟粕。

### 第5章 语法

#### 5.1 语句和表达式

类比英语，“句子”是完整表达某个意思的一组词，由一个或多个“短语”组成。

JavaScript的语法也是如此。语句相当于句子，表达式相当于短语，运算符则相当于标点符号和连接词。

##### 5.2 运算符优先级

&& 和 || 运算符返回它们其中一个操作数的值，而非true或false。

```js
var a = 42;
var b = 'foo';

a && b; // 'foo'
a || b; // 42
```

更强的绑定

```js
a && b || c ? c || b ? a : c && b : a
```

优先级是

```js
(a && b || c) ? (c || b) ? a : (c && b) : a
```

&& 优先级高于||，而||的优先级又高于? :。

小结：

语句和表达式在英语中都能找到类比——语句就像英语中的句子，而表达式就像短语。表 达式可以是简单独立的，否则可能会产生副作用。

## 第二部分 异步和性能

### 第1章 异步：现在与将来

程序中现在运行的部分和将来运行的部分之间的关系就是异步编程的核心。

一直以来，大家以为回调函数已经够用了，但是随着JavaScript的规模和复杂性的持续增长，这个方式已经不能满足。

### 第2章 回调

回调是JavaScript这门语言最基础的异步模式。随着异步编程领域的发展，回调已经不够用了。

### 第3章 Promise

Promise 是一种封装和组合未来值的易于复用的机制。

#### 3.1 Promise 模式

Promise.all([ .. ])

在经典的编程术语中，门(gate)是这样一种机制要等待两个或更多并行 / 并发的任务都 完成才能继续。它们的完成顺序并不重要，但是必须都要完成，门才能打开并让流程控制继续。

```js
// request(..)是一个Promise-aware Ajax工具
// 就像我们在本章前面定义的一样
var p1 = request( "http://some.url.1/" );
var p2 = request( "http://some.url.2/" );
Promise.all( [p1,p2] ) .then( function(msgs){
  // 这里，p1和p2完成并把它们的消息传入
  return request("http://some.url.3/?v=" + msgs.join(",") );
})
.then( function(msg){
  console.log( msg );
});
```

Promise.all([ .. ])需要一个参数，是一个数组，通常由Promise实例组成。从Promise. all([ .. ]) 调用返回的 promise 会收到一个完成消息(代码片段中的 msg)。这是一个由所 有传入 promise 的完成消息组成的数组，与指定的顺序一致(与完成顺序无关)。

Promise.race([ .. ])

尽管Promise.all([ .. ])协调多个并发Promise的运行，并假定所有Promise都需要完

成，但有时候你会想只响应“第一个跨过终点线的 Promise”，而抛弃其他 Promise。 这种模式传统上称为门闩，但在 Promise 中称为竞态。

```js
// request(..)是一个支持Promise的Ajax工具
// 就像我们在本章前面定义的一样
var p1 = request( "http://some.url.1/" );
var p2 = request( "http://some.url.2/" );
Promise.race( [p1,p2] ) .then( function(msg){
// p1或者p2将赢得这场竞赛
  return request("http://some.url.3/?v=" + msg );
})
.then( function(msg){
  console.log( msg );
});
```

#### 3.2 Promise API概述

```js
var p = new Promise( function(resolve,reject){
  // resolve(..)用于决议/完成这个promise
  // reject(..)用于拒绝这个promise
});
```

构造器 Promise(..) 必须和 new 一起使用，并且必须提供一个函数回调。这个 回调是同步的或立即调用的。这个函数接受两个函数回调，用以支持 promise 的决议。通 常我们把这两个函数称为 resolve(..) 和 reject(..):

#### 3.3 Promise 局限性

##### 3.3.1 顺序错误处理

```js
var p = foo( 42 ) .then( STEP2 ) .then( STEP3 );
```

这里可能有点鬼祟、令人迷惑，但是这里的 p 并不指向链中的第一个 promise(调用 foo(42) 产生的那一个)，而是指向最后一个 promise，即来自调用 then(STEP3) 的那一个。

这个 Promise 链中的任何一个步骤都没有显式地处理自身错误。这意味着你可以在 p 上注册一个拒绝错误处理函数，对于链中任何位置出现的任何错误，这个处理函数都会 得到通知:

```js
p.catch( handleErrors );
```

##### 3.3.2 单一值

Promise 只能有一个完成值或一个拒绝理由。在简单的例子中，这不是什么问题，但是在更复杂的场景中，你可能就会发现这是一种局限了。

##### 3.3.3 单决议

Promise 只能被决议一次(完成或拒绝)。在许多异步情况中，你只会获取一个值一次，所以这可以工作良好。

##### 3.3.4 惯性

要在你自己的代码中开始使用 Promise 的话，一个具体的障碍是，现存的所有代码都还不理解 Promise。

##### 3.3.5 无法取消的 Promise

一旦创建了一个 Promise 并为其注册了完成和 / 或拒绝处理函数，如果出现某种情况使得这个任务悬而未决的话，你也没有办法从外部停止它的进程。

小结：

Promise 非常好，请使用。它们解决了我们因只用回调的代码而备受困扰的控制反转问题。

### 第4章 生成器

#### 4.1 生成器产生值

```js
var a = [1,3,5,7,9];
var it = a[Symbol.iterator]();
it.next().value; // 1
it.next().value; // 3
it.next().value; // 5
```

```js
function *something() {
  var nextVal;
  while (true) {
    if (nextVal === undefined) {
      nextVal = 1;
    }
    else {
      nextVal = (3 * nextVal) + 6;
  }
      yield nextVal;
  }
}

for (var v of something()) {
  console.log( v );
  // 不要死循环!
  if (v > 500) {
    break;
  }
}
// 1 9 33 105 321 969
```

#### 4.2 异步迭代生成器

```js
function foo(x, y) {
    ajax(
        "http://some.url.1/?x=" + x + "&y=" + y, function (err, data) {
            if (err) {
                // 向*main()抛出一个错误 it.throw( err );
            }
            else {
                // 用收到的data恢复*main()
                it.next(data);
            }
        });
}

function* main() {
    try {
        var text = yield foo(11, 31);
        console.log(text);
    }
    catch (err) {
        console.error(err);
    }
}
var it = main();
// 这里启动! it.next();
```

在yield foo(11,31)中，首先调用foo(11,31)，它没有返回值(即返回undefined)，所以 我们发出了一个调用来请求数据。

从本质上而言，我们把异步作为实现细节抽象了出去，使得我们可以以同步顺序的形式追 踪流程控制:“发出一个 Ajax 请求，等它完成之后打印出响应结果。”并且，当然，我们 只在这个流程控制中表达了两个步骤，而这种表达能力是可以无限扩展的，以便我们无论 需要多少步骤都可以表达。

小结：

在异步控制流程方面，生成器的关键优点是:生成器内部的代码是以自然的同步 / 顺序方 式表达任务的一系列步骤。其技巧在于，我们把可能的异步隐藏在了关键字 yield 的后面， 把异步移动到控制生成器的迭代器的代码部分。

你不知道的JavaScript中卷主要介绍了类型语法与异步,这两章详细的说明了JavaScript的细节，对于特定场景的处理，可以更加游刃有余。你不知道的JavaScript下卷主要对于上中卷的回顾与总结，故不作解读。
