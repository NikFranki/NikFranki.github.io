---
layout:     post
title:      "Generator从使用到实现"
subtitle:   ""
date:       2020-04-20
author:     "franki"
header-img: "images/post-bg-js-module.jpg"
tags:
    - Generator
---

* 目录
{:toc #markdown-toc}

最近事情有点多，博客已经减少到一个月一篇了，作为一个对于技术有持续追求的人来说，学无止境应该是每一个技术人的座右铭！应该像岳飞背上’精忠报国‘的四个大字一样，时刻警示告知自己，再忙也不能忘记给自己充电。不过无论如何，饭还得一口一口吃，不能一口气吃成一个大胖子。

终于可以写一篇关于 generator 的文章，这个东西一直困扰太久太久，从2016年接触 es6 开始，看到 generator 的使用时总是一脸懵逼，话说当时看阮一峰大神这部分的讲解，也是一知半解的，最后索性就放弃了，所以自然在我的项目中也从未出现过，一直到现在。。。，都2020年，连这个都不了解，是不是太 low，于是下定决心，写一篇关于 generator 的解读文章，从使用到源码。

> 生成器(Generator)是 ECMAScript6 中引入的新概念。生成器本身是一个强大的工具，可以在日常开发中高效的完成一些任务。

与生成器相关的两个概念

- 生成器函数： 用来创建生成器对象的一类特殊函数
- 生成器对象： 生成器函数的具体实例

生成器最大的特点是在于它们可以被暂停和继续。在普通的 JavaScript 的函数，我们必不能控制函数的执行，一旦执行就会到执行结束。考虑到 JavaScript 是单线程，在函数的执行过程中，是不可以终止函数执行的，一旦陷入无限循环，整个应用都会被阻塞。

最简单的生成器函数

```js
function *eg1() {
  yield 1;
  yield 2;
  yield 3;
}
```

在调用 eg1 的生成器函数之后，得到的是一个生成器对象。生成器对象的 next 方法被调用时，可以依次返回多个不同的值。eg1 中 yield 1 yield 2 yield 3 共3个值。当 next 方法调用1 2 3会被依次返回。就像下面的描述一样

```js
let func = eg1();
func.next(); // {value: 1, done: false}
func.next(); // {value: 2, done: false}
func.next(); // {value: 3, done: false}
func.next(); // {value: undefined, done: true}
```

第一次的 next 调用时，会执行到第一个 yield 表达式即是 yield 1，并把结果1返回给调用者。返回值包括了两个属性 value 和 done 的对象。属性 value 是 yield 表达式产生的值，而 done 则是表示是否还有更多的值可以被获取。再次执行 next 方法就到第二个 yield 表达式。如此反复，直到第四个，done 的值被置为 true，value 为  undefined，表示没有更多的值可以获取了。

生成器的强大之处就是可以暂停和继续生成器对象执行的能力。每个生成器对象都是一个状态机。每个对象都在内部维护自己的状态，彼此互不影响。调用 next 方法会继续生成器的执行，触发内部的状态转换，运行到下一个  yield 表达式所在的位置。执行将会被暂停，等待下次 next 方法的调用。如下面代码所示：

```js
let func1 = eg1();
let func2 = eg1();
func1.next(); // {value: 1, done: false}
func2.next(); // {value: 1, done: false}p
func1.next(); // {value: 2, done: false}
```

### 基本使用

#### 调用 next 方法

```js
function *eg2() {
  let x = yield 1;
  let y = yield x + 10;
  let z = yield y * 10;
}
```

一眼看上去，很像调用 next 之后x对应的值是1，y 对应的值是11，z 对应的值是110。

但是实际的结果却并不如此。实际的值分别是1，NaN和NaN。产生这样的结果原因是，调用 next 方法所传递的参数值，被作为上一个 yield 表达式的值。由于我们在调用 next 时没有传入对应的参数值，每个 yield 表达式的实际值都为 undefined。在第一个 next 调用时，由于没有上一个 yield 表达式，因此该值被忽略。第二个 next 方法调用时，上一个 yield 表达式，也就是 yield 1的值是 next 调用时的参数值，也就是 undefined，因此 x 的值  undefined，所以 yield x+10 的值是NaN。由此类比，y 是 undefined，因此z也是NaN。

结果是：

```js
let func = eg2();
func.next(); // {value: 1, done: false}
func.next(); // {value: NaN, done: false}
func.next(); // {value: NaN, done: false}
func.next(); // {value: undefined, done: true}
```

下面我们尝试调用 next 方法时传值。第二个 next 调用时传入值1，所以1被作为上一个 yield 表达式 yield 1的值，把 x 设为1，所以该 next 调用后产生的值为11；第三个 next 调用时，传入2，因此2作为第二个 yield 表达式 yield x+10，把y设为2，所以该 next 方法调用后产生的值为20。调用过程如下：

```js
let func = eg2();
func.next(); // {value: 1, done: false}
func.next(1); // {value: 11, done: false}
func.next(2); // {value: 20, done: false}
func.next(3); // {value: undefined, done: true}
```

#### 使用 return 语句

```js
function *eg3() {
  let x = yield 1;
  return x + 1;
}

let func = eg3();
func.next(); // {value: 1, done: false}
func.next(1); // {value: 2, done: true}
func.next(); // {value: undefined, done: true}
```

在生成器函数中，可以使用 return 语句。return 语句可以返回值会传递给 next 调用者，同时会结束掉生成器对象的执行。

#### 生成器与迭代器

如上所见，生成器函数对象 next 方法的返回值并不是很直观，需要通过 done 属性来判定是否接下来还有值。事实是生成器对象也是迭代器对象，而迭代器对象用next方法来获取其中的值。生成器对象也是可迭代的。es6 中的for of， spread 操作符和集合类型都可以操作遍历其中的值。

```js
function *eg4() {
  yield 'a';
  yield 'b';
  yield 'c';
}

// for of
for (let i of eg4()) {
  console.log(i);
}
// 输出 a b c

// spread操作符
[...eg4()]
// 输出 ['a', 'b', 'c']

// set 操作
new Set(eg4()).size;
// 输出3
```

### 生成器的高级用法

#### 生成器对象的 return 方法

```js
function *eg5() {
  yield 'a';
  yield 'b';
  yield 'c';
}

let func = eg5();
func.next(); // {value: 'a', done: false}
func.return('d'); // {value: 'd', done: true}
func.next(); // {value: undefined, done: true}
```

生成器对象的 return 方法可以用来返回值并结束生成器函数的执行。

#### 生成器对象的 throw 方法

```js
function *eg6() {
  let x = yield 1;
  let y = yield x + 1;
  let z = yield y + 2;
}

let func = eg6();
func.next(); // {value: 1, done: false}
func.next(1); // {value: 2, done: false}
func.throw('hello'); // Uncaught hello
func.next() // {value: undefined, done: true}
```

生成器对象的 throw 方法可以用来传入一个值，并使其抛出异常。throw 和 next 都可以传入值到生成器对象来改变其行为，通过 next 传入的值被当做上一个 yield 表达式的值，而通过 throw 传入的值则相当于上一个 yield 语句被替换成 throw 语句。如果抛出的异常没有被处理，会被传递到 JavaScript 引擎，导致生成器的执行终止。

#### try-catch 捕获异常

```js
function *eg7() {
  yield 1;
  try {
    yield 2;
  } catch(e) {
    console.log(e);
  }
  yield 3;
  yield 4;
}

let func = eg7();
func.next(); // {value: 1, done: false}
func.next(); // {value: 2, done: false}
func.throw('lala'); // lala {value: 3, done: false}
func.next(); // {value: 4, done: false}
```

可以使用 try-catch 来捕获异常。throw 语句会替换上一个 yield 表达式这里的话就是 yield 2。抛出的对象由 try-catch 进行处理了。

#### 使用 yield* 表达式

```js
function *eg8() {
  yield* [1, 2, 3];
}

for (let item of eg8()) {
  console.log(item);
}
// 依次输出1 2 3
```

使用yield*表达式可以来生成一个值的序列。当使用yield\*可以将生成器对象的序列生成被代理给另外一个生成器对象或者可迭代对象。

#### 使用多个 yield* 表达式

```js
function *eg9() {
  yield* [1, 2, 3];
  yield 'x';
  yield* 'hello';
}
for (let item of eg9()) {
  console.log(item);
}
// 1, 2, 3, x, h, e, l, l, o
```

yield 后面可以跟*，表示一个可迭代的对象。yield\* 也是一个表达式，可以通过在生成器函数中使用 return 来控制最后一个输出的结果。如下代码所示：

```js
let res;

function loop(iteratable) {
  for (let value of iteratable) {
    // ignore
  }
}

function *abc() {
  yield* 'abc';
  return 'd';
}

function *generator() {
  res = yield* abc();
}

loop(generator());
console.log(res); // d

```

#### 嵌套的 yield 表达式

```js
function *eg10() {
  yield yield yield 1;
}
// 1 undefined undefined
```

最内层的 yield 表达式生成值是1，然后中间的 yield 表达式生成yield 1的值，由于 next 方法没有传入参数，即为 undefined，最外层的 undefined 也是如此。

简单的生成器实现：

```js
function generator(cb) {
  var object = {
    next: 0,
    stop: function() {}
  };
  
  return (function() {
  return {
      next: function() {
        var ret = cb(object);
        if (ret === undefined) return {value: undefined, done: true};
        return {value: ret, done: false};
      }
    }
  })();
}

// 经过babel编译过后的eg1函数
function eg1() {
  return generator(function(_context) {
    while(1) {
      switch(_context.prev = _context.next) {
             case 0:
              _context.next = 2;
              return 1;
             case 2:
              _context.next = 4;
              return 2;
             case 4:
              _context.next = 6;
              return 3;
             case 6:
             case "end":
              return _context.stop();
      }
    }
  });
}

var t = eg1();
t.next(); // {value: 1, done: false}
t.next(); // {value: 2, done: false}
t.next(); // {value: 3, done: false}
t.next(); // {value: undefined, done: true}
```

小结：

ECMAScript 6 中的生成器是一个强大的工具。目前很多的JavaScript框架都有运用。本章具体讲述了 generator 的一些概念以及使用。大家可以在平常的工作中根据自己的需要使用这部分的内容。
