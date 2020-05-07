---
layout:     post
title:      "细读你不知道的JavaScript上卷"
subtitle:   "发现未知"
date:       2020-01-20
author:     "franki"
header-img: "images/posts/javascript/you-dont-javascript-1.jpg"
catalog: true
tags:
  - 前端开发
  - JavaScript
---

## 第一部分 作用域和闭包

### 第1章 作用域是什么

几乎所有编程语言最基本的功能，就是能够提供存储变量当中的值，并且可以对这个值进行访问或修改。***但是有没有想过这些问题？***这些变量存储在哪里？程序如何找到它们？

这些问题需要一套设计良好的规则来存储变量，并且之后可以方便地找到这些变量。这套规则就叫做**作用域**。

接着，思考下在哪里制定作用域规则呢？

答案揭晓前，先来了解下，

1.1 编译原理：

大部分的编程语言，特别是传统的编程语言，都是提前编译的，通常JavaScript被归类于“动态”或者“解释执行”语言，但是事实上它的确是一门编译语言。

JavaScript引擎进行编译的步骤和传统的编程语言非常相似，在某些细节方面表现地更加复杂。

传统的编译语言的流程中，都要经历三个步骤。统称为“编译”。

* 分词/词法分析（Tokenizing/Lexing）

  由字符组成的字符串会被分解成有意义的代码块，这些代码块又称为单元（token），eg：var a = 2;。这段程序会被分解为这些词法单元：var、a、=、2、；。

* 解析/语法分析（Parsing）

  将词法单元流（数组）转换成一个由元素逐级嵌套所组成的代表了程序语法结构的树。这个数被叫做“抽象语法树”（Abstract Synatax Tree, AST），var a = 2;的抽象语法树中可能会有一个叫做VariableDeclaration的顶级节点，以及一个叫做AssignmentExpression的子节点。AssignmentExpression节点有一个叫做NumbericLiteral（值为2）的子节点。

* 代码生成

  将AST转换为可执行代码的过程被称为代码生成。这个过程与语言、目标平台等息息相关。

  简单来说，就是把var a = 2;的AST转化为一组机器指令，用来创建一个叫做a的变量（包括分配内存），并将一个值存在a中。

上面讲的是大部分编程语言会经历的，对于JavaScript来说，除了以上，还会在词法分析、语法分析阶段进行性能优化，说了以上步骤，你应该懂得了作用域规则在哪里和什么时候制定了的。

1.2 如何理解作用域

来看看以下的对话

演员表

* 引擎

  负责JavaScript程序的编译与执行过程

* 编译器

  负责词法分析及代码生成

* 作用域

  负责收集并维护所有声明的标识符（变量）组成的一系列查询，并实施一套非常严格的规则，确定执行的代码对这些标识符有访问权限。

eg： var a = 2;

首先引擎会把这段程序分解为词法单元，然后将词法单元解析为树结构（AST）。

编译器对这段代码进行生成，一般认为是“为一个变量分配一个内存，将其命名为a，然后将值2保存进这个变量”。

事实却不并完全这样：

编译器会这样做：

1. 遇到var a,编译器会询问作用域是否存在该名称存在于同一个作用域的集合中，如果是，编译器会忽略该声明，继续进行编译；否则它会要求作用域在当前作用域集合中声明一个新变量，并命名为a；
2. 接下来编译器会声明所需的代码，这些代码用来处理a = 2 这个赋值操作。引擎首先会问作用域，是否存在这样的变量a，如果是，引擎就会使用该变量，并进行赋值操作；如果否，引擎会继续查找该变量。

理解LHS查询、RHS查询

LHS查询试图查找变量的容器本身，从而可以对其赋值。

RHS查询取到它的原值。

简单来说a = 2；就是LHS查询，首先看a是否存在于作用域中，如果找到就可以对其赋值；console.log(a);就是要得到a的值。

看看以下程序，既存在LHS又存在RHS

```js
function foo(a) {
  console.log(a);
}
foo(2);
```

最后一行foo(...)函数的调用对foo进行RHS引用，意味着“去找到foo的值，并把它给我”。

代码中隐式的a = 2操作很可能被忽略掉。这个操作发生在2被当做参数传递给foo(...)函数时，2会被分配给参数a，需要进行一次LHS查询。

1.3 作用域嵌套

当一个块或者函数在另一个块或函数中，就发生了作用域嵌套。因此，在当前作用域无法找到该变量时，引擎会在外层作用域继续查找，直到找到该变量。或者抵达最外层的作用域为止。

看看下面的代码：

```js
function foo(a) {
  console.log(a + b);
}

var b = 2;

foo(2); // 4
```

对b进行RHS查询时，在当前作用域foo函数内无法找到，但可以在上一级作用域找到。

1.4 异常

为啥一定要区分LHS和RHS？

变量在未声明之前，两种查询的行为是不一致的。

eg：

```js
function foo(a) {
  console.log(a + b);
  b = a;
}

foo(2);
```

第一次对b进行RHS查询是无法查找到改变量的（什么作用域都不存在此变量）。

如果RHS查询在所有嵌套的作用域中遍寻不到所需的变量，引擎会跑出ReferenceError异常.

相较之下,当引擎进行LHS查询时，如果在顶层无法查找目标变量，就会在全局作用域中创建这样的一个具有该名称的变量，并将返还给引擎，前提是在非严格模式下。

还有一个点就是当RHS找到该变量，如果对其进行不合理的操作，例如a是基本类型，但是却对其进行函数调用操作，则会报TypeError的异常。

小结：

作用域是一套规则；用于确定在如何查找变量（标识符），如果查找的结果是要对其赋值，那么就会进行LHS重新，否则就进行RHS查询；

var a = 2; 会被分解为以下2个过程；

1. var a 在其作用域声明新变量，这会发生在代码执行前。
2. a = 2会进行LHS查询，并对其进行赋值。

LHS和RHS查询都会在当前作用域中执行，如果有需要，才会向上级作用域继续查找目标标识符（变量），最后抵达全局作用域（顶层），没有找到则停止。

不成功的RHS查询会抛出ReferenceError异常。不成功的LHS查询会自动隐式地创建一个全局变量。该变量使用LHS引用的目标作为标识符。

### 第2章 词法作用域

2.1 词法阶段

大部分标准编程语言编译器的第一个阶段都是词法化。

词法作用域是定义在词法阶段的作用域。换句话说，词法作用域是由你在写代码将变量和块作用域写在哪里决定的。

eg：

```js
function foo(a) {
  var b = a * 2;

  function bar(c) {
    console.log(a, b, c);
  }
  
  bar(b * 3);
}

foo(2); // 2 4 12
```

这个例子中有三个逐级嵌套的作用域。

* 全局作用域，只有一个标识符：foo
* 包含中foo创建的作用域，有三个标识符：a b bar
* 包含bar创建的作用域，只有一个标识符：c

查找：

首先从最内部的作用域，也就bar(...)函数的作用域开始查找。引擎无法找到a，会继续往上查看，在foo(...)找到了a，其他的标识符类似的查找。

作用域会在第一个匹配找到的情况下，停止查找。

2.2 欺骗语法

2.2.1 eval

```js
function foo(str, a) {
  eval(str); // 欺骗
  console.log(a, b);
}

var b = 2;

foo("var b = 3;", 1); // 1, 3
```

eval(...)调用中的“var b = 3”会被当做在那里一样来处理。由于声明了一个新的变量b，因此它对于已经存在的foo(...)词法作用域进行了修改。事实上foo内部创建了一个变量b，并遮蔽了外部作用域的变量b。

2.2.2 with

``` javascript
function foo(obj) {
  with(obj) {
    a = 2;
  }
}

var o1 = {
  a: 3
};

var o2 = {
  b: 3
};

foo(o1);
console.log(o1.a); // 2

foo(o2);
console.log(o2.a); // undefined
console.log(a); // 2 a被全局泄露到全局作用域上了
```

看起来只是对a的变量进行简单的词法引用，实际上是一个LHS引用，并将2赋值给它。o2并没有a属性，a = 2的时候就创建了一个全局的变量a。

小结：

词法作用域意味着是由书写代码时函数声明的位置来决定的。编译的词法分析阶段基本能够知道全部标识符在哪里并且是如何声明的。

JavaScript中有两个机制可以欺骗词法作用域：eval(...)和with。前者可以对一段代码字符串进行演算，并借此来修改已经存在的词法作用域。后者是通过一个对象的引用当做作用域来处理，将队形的属性当做作用域的标识符来处理，从而创建了一个新的词法作用域。

### 第3章 函数作用域和块作用域

3.1 函数作用域

在任意代码片段外部添加包装函数，可以将内部的变量和函数定义“隐藏”起来，外部作用域无法访问包装函数内部的任何内容。

```js
var a = 2;

function foo() {
  var a = 3;
  console.log(a); // 3
}

foo();

console.log(a); // 2
```

以上的代码可以内部变量隐藏起来，只有当前作用域才可以访问（foo）,外部作用域无法访问包装函数内部的任何内容。

这里存在一些问题，比如说，要访问foo里面的变量a，那么一定需要foo()调用，意味着foo这个名称本身“污染了”所在的作用域（这里的作用域是全局作用域）。

JavaScript提供这种解决方式

```js
var a = 2;

(function () {
  var a = 3;
  console.log(a); // 3
})();

console.log(a); // 2
```

3.1.1 匿名和具名

```js
setTimeout(function() {
  console.log('I waited 1 second!');
}, 1000)
```

这个就叫做匿名函数表达式。没有函数名称。

匿名函数表达式书写简单，但是也有缺点：

* 没有函数名，在栈追踪中不会显示出函数名，很难调试
* 当需要调用自身的时候，只能通过过期了的arguments.callee引用。
* 可读性/可理解性下降

一般可以这样处理：

```js
setTimeout(function timeoutHandler() {
  console.log('I waited 1 second!');
}, 1000);
```

3.1.2 立即执行函数表达式

```js
var a = 2;

(function foo() {
  var a = 3;
  console.log(a); // 3
})();

console.log(a); // 2
```

社区用语：IIFE(Immediately Invoked Function Expression);

函数被()包裹，在末尾加上另外一个()可以立即执行这个函数

进阶用法：

```js
var a = 2;

(function foo(global) {
  var a = 3;
  console.log(a); // 3
  console.log(global.a); // 2
})(window);

console.log(a); // 2
```

从外部传递参数进去

```js
var a = 2;

(function IIFE(def) {
  def(window);
})(function def(global) {
  var a = 3;
  console.log(a); // 3
  console.log(global.a); // 2
});
```

3.2 块作用域

在JavaScript常见的都是函数作用域，当时其他的作用域也是存在的，通过这些类型的作用域可以实现维护起来更加优秀、简洁的代码。

想想这块代码

```js
for (var i=0; i<10; i++) {
  console.log(i);  
}
```

变量会被定义在全局作用域中，如果在某个地方便便了变量i的值，可以会使事情变得复杂无比。

3.2.1 with

前面说过，with可以创建出作用域

3.2.2 try/catch

```js
try {
  undefined();
} catch(err) {
  console.log(err); // TypeError: undefined is not a function
}

console.log(err); // ReferenceError: err is not defined
```

3.2.3 let

```js
var foo = true;

if (foo) {
  let bar = foo * 2;
  console.log(bar); // 2
}

console.log(bar); // ReferenceError: bar is not defined
```

3.2.4 const

```js
var foo = true;

if (foo) {
  var a = 2;
  const b =3;
  
  a = 2;
  b = 4;
}

console.log(a); // 3
console.log(b); // TypeError: Assignment to constant variable.
```

小结：

函数作用域是JavaScript最常见的作用域单元

with、try/catch、let、const都可以创建作用域单元

块作用域和函数作用域的同时存在，一定程度上可以改善代码，使得代码更加可读、可维护。

### 第4章 提升

4.1 先有🐔还是现有🥚

```js
a = 2;

var a;

console.log(a); // 2
```

输出是2

```js
console.log(a); // undefined

var a = 2;
```

输出的是undefined

4.2 编译器再度来袭

上面的两端代码公共点都是要经历两个阶段，一是编译阶段，而是执行阶段。

编译阶段会把所有的声明，用合适的作用域关联起来。

var a = 2;可以看成是var a; a = 2;两个阶段。

上面的第一段代码会被这样处理：

```js
var a;

a = 2;
console.log(a); // 2
```

第二段代码会这样处理：

```js
var a;

console.log(a); // undefined

a = 2;
```

结论是：先有蛋（声明）再有鸡（赋值）

再看看这段代码：

```js
foo();

function foo() {
  console.log(a); // undefined
  var a = 2;
}
```

foo函数声明也会被提升。

会被JavaScript编译为：

```js
function foo() {
  var a;
  console.log(a); // undefined
  a = 2;
}

foo();
```

相反地：

```js
foo(); // 不是ReferenceError，而是TypeError
var foo = function() {
  //...
}
```

foo作为var被提升了，函数表达式却没有被提升，且foo()，foo不是一个函数而进行函数调用故报错为TypeError。

4.3 函数优先

```js
foo(); // 1

var foo;

function foo() {
  console.log(1);
}

foo = function() {
  console.log(2);
};
```

输出的是1而不是2

这个会被引擎理解为：

```js
function foo() {
  console.log(1);
}

foo();

foo = function() {
  console.log(2);
};
```

尽管var foo出现在function foo()..声明之前，但它是重复的声明（因此被忽略），因为函数声明会被提升到普通变量之前。

小结：

js在代码执行前会经历代码编译阶段。

函数声明和变量声明会被提升到作用域的顶层。

### 第5章 作用域闭包

开门见山

**闭包定义：当函数可以记住并访问所在的词法作用域时，就产生了闭包，即使函数是在当前的词法作用域之外执行。**

5.1 循环和闭包

```js
for (var i=0; i<=5; i++) {
  setTimeout(function timer() {
    console.log(i);
  }, i*1000);
}
```

经典的问题，这里面最终输出的都是6

延迟函数的回调会在循环结束时才执行，循环终止的条件是i=6，所以会输出6次的6

解决方式1

```js
for (var i=0; i<=5; i++) {
  (function() {
    var j = i;
    setTimeout(function() {
      console.log(j);
    }, j*1000);
  })();
}
```

IIFE会产生新的词法作用域，每次迭代用j去储存i的值。

解决方式改进：

```js
for (var i=0; i<=5; i++) {
  (function(j) {
    setTimeout(function() {
      console.log(j);
    }, j*1000);
  })(i);
}
```

可以将i传递进去，每次迭代产生一个新的作用域，使得延迟函数的回调可以将新的作用域封闭在每个迭代内部，每个迭代中都会含有一个具有正确的变量供我们使用。

解决方式2

```js
for (let i=0; i<=5; i++) {
  setTimeout(function() {
    console.log(i);
  }, i*1000);
}
```

let 本身会产生块级作用域，每次迭代都会声明。

5.2 模块

```js
var foo = (function(id) {
  function change() {
    publicAPI.identify = identify2;
  }
  
  function identify1() {
    console.log(id);
  }
  
  function identify2() {
    console.log(id.toUpperCase());
  }
  
  var publicAPI = {
    change: change,
    identify: identify1
  }
  
  return publicAPI;
})('foo module');

foo.identify(); // foo module
foo.change();
foo.identify(); // FOO MODULE
```

通过模块实例的内部保留对公共API对象的内部引用，可以从内部对模块实例进行修改，包括添加或者删除方法和属性，以及修改它们的值。

现代模块化机制

大多数模块依赖加载器/管理器本质上都是将这种模块定义封装进一个友好的API。

```js
var MyModules = (function Manager() {
  var modules = {};
  
  function define(name, deps, impl) {
    for (var i=0; i<deps.length; i++) {
      deps[i] = modules[deps[i]];
    }
    modules[name] = impl.apply(impl, deps);
  }
  
  function get(name) {
    return modules[name];
  }
  
  return {
    define: define,
    get: get
  }
})();
```

这段代码的核心是modules[name] = impl.apply(impl, deps); 为了模块的定义引入了包装器函数，并且返回值，存储在一个根据文字来管理的模块列表中。

可以这样使用:

```js
MyModules.define('bar', [], function() {
  function hello(who) {
    return 'Let me introduce: ' + who;
  }
  
  return {
    hello: hello
  };
});

MyModules.define('foo', ['bar'], function() {
  var hungry = 'hippo';
  
  function awesome() {
    console.log(bar.hello(hungry).toUpperCase());
  }
  
  return {
    awesome: awesome
  };
});

var bar = MyModules.get('bar');
var foo = MyModules.get('foo');
console.log(bar.hello('hippo'));
foo.awesome(); // LET ME INTRODUCE: HIPPO
```

'foo'和'bar'都是通过一个返回的公共API的函数来定义的。

ES6除了新的模块机制，可以在编译器期间知道导入的模块的API是否真实存在，可以运行时掏出错误。

小结：

闭包，工作的工作原理，保留函数当前词法作用域的引用，以至于在任何在任何时候调用的时候都可以访问。

模块有两个特征：1 创建内部作用域而调用一个包装函数 2 包装函数返回值至少包括一个对内部函数的引用，这样会覆盖整个包装函数内部作用域的闭包。

JavaScript 只有静态作用域（词法作用域）不存在作用域

## 第二部分 this和对象原型

### 关于this

this是函数在代码中被调用的位置决定的

### this全面解析

### 绑定规则

#### 2.1 默认绑定

```js
function foo() {
  console.log(this.a);
}

var a = 2;

foo(); // 2
```

这是一个独立函数调用，var a = 2相当于在全局定义了一个变量a值为2，调用foo()时，this.a被解析成了全局变量a。这时this就是应用了默认绑定。这时的this指向全局对象。（在严格模式下不适用此规则）

#### 2.2 隐式绑定

这条规则需要考虑的规则是调用位置是否有上下文对象，或者说是否被某个对象拥有或者包含。

```js
function foo() {
  console.log(this.a);
}

var obj = {
  a: 2,
  foo: foo
};

obj.foo(); // 2
```

首先需要注意的是foo()的声明方式，及其之后是如何被当做引用属性添加到obj中的。但是无论是直接在obj中定义还是先定义还是先定义再添加为引用属性，这个函数严格来说都不属于obj对象。

然而，调用位置会使用obj上下文来引用函数，因此你可以说函数被调用时obj对象“拥有”或者“包含”它。

隐式绑定规则会把函数调用中的this绑定到这个上下文对象。因为调用foo()时this被绑定到obj，因此this.a和obj.a是一样的。

对象属性引用链最后最顶层会影响调用位置。

```js
function foo() {
  console.log(this.a);
}

var obj2 = {
  a: 42,
  foo: foo
};

var obj1 = {
  a: 2,
  obj2: obj2
};

obj1.obj2.foo(); // 42
```

隐式丢失this

```js
function foo() {
  console.log(this.a);
}

var obj = {
  a: 2,
  foo: foo
};

var bar = obj.foo;

var a = 'oops, global'; // a 是全局对象的属性

bar(); // 'oops, global'
```

虽然bar是obj.foo的一个引用，但是实际上，它引用的是foo函数本身，因此此时的bar()其实是一个不带任何修饰的函数调用，因此应用了默认绑定。

函数传参丢失this

```js
function foo() {
  console.log(this.a);
}

var obj = {
  a: 2,
  foo: foo
};

function bar(fn) {
  fn(); // 相当于引用的是foo
}

var a = 'opps, global';

bar(obj.foo); // 'opps, global'
```

#### 2.3 显式绑定

javascript的函数都可以通过call、apply方法来改变函数的this指向。

```js
function foo() {
  console.log(this.a);
}

var obj = {
  a: 2
};

foo.call(obj); // 2
```

通过foo.call(..)把this绑定到obj上。

如果这个时候call里面的参数传入了原始值，会进行装箱操作（new String() new Boolean() new Number()）。

硬绑定

```js
function foo(something) {
  return this.a + something;
}

function bind(fn, ctx) {
  return function() {
    fn.apply(ctx, arguments);
  }
}

var obj = {
  a: 2
};

var bar = bind(foo, obj);

var b = bar(3);
console.log(b); // 5
```

#### 2.4 new绑定

使用new来调用函数，或者说发生构造函数调用时，会自动执行下面的操作。

1. 创建一个全新的对象
2. 这个新对象被执行原型连接
3. 这个新对象会绑定到函数调用的this
4. 如果函数没有返回其他对象，那么new表达式中的函数调用会自动返回这个新对象

模拟new操作

```js
function newFn(fn) {
  var obj = Object.create(fn);
  obj.fn = fn;
  var args = [].slice.call(arguments, 1);
  return typeof obj.fn.apply(obj, args) === 'object' ? obj.fn.apply(obj, args) : obj;
}
```

真正new

```js
function foo(a) {
  this.a = a;
}

var bar = new foo(2);
console.log(bar.a); // 2
```

使用new来调用foo(..)时，会构造一个新对象并把它绑定到foo(..)调用中的this上。new是最后一种可以影响函数调用时绑定行为的方法。

### 优先级

```js
function foo() {
  console.log(this.a);
}

var obj1 = {
  a: 2,
  foo: foo
};

var obj2 = {
  a: 3,
  foo: foo
};

obj1.foo(); // 2
obj2.foo(); // 3

obj1.foo.call(obj2); // 3
obj2.foo.call(obj1); // 2
```

可以看到显式绑定比隐式绑定优先级更高。

```js
function foo(something) {
  this.a = something;
}

var obj1 = {
  foo: foo
};

var obj2 = {};

obj1.foo(2);
console.log(obj1.a); // 2

obj1.foo.call(obj2, 3);
console.log(obj2.a); // 3

var bar = new obj1.foo(4);
console.log(obj1.a); // 2
console.log(bar.a); // 4
```

由此可以看出，new的优先级比隐式绑定的优先级高

注意：使用call、apply传入null、undefined使用默认绑定规则。

小结：

如果要判断一个运行中函数的this绑定，就需要找到这个函数的直接调用位置。找到之后就可以顺序应用下面这四条规则来判断this的绑定对象。

1. 由new调用？绑定到新创建的对象
2. 由call或者apply（或者bind）调用？绑定到指定的对象。
3. 由上下文对象调用？绑定到那个上下文对象。
4. 默认：在严格模式下绑定到undefined，否则绑定到全局对象。

如果不想出现意外绑定的情况，可以使用Object.create(null)，以保护全局对象。

es6的箭头函数不适用上述的四种情况，而是根据当前的词法作用域来决定this，具体来书，箭头函数会继承外层函数调用的this绑定。

### 对象

#### 3.1 对象到底是什么呢

大概是这样？

```js
var myObj = {
  key: value
  // ...
};
```

或者又是这样？

```js
var myObj = new Object();
myObj.key = value;
```

改造方式和文字形式生成的对象是一样的。唯一的区别是，是文字声明中可以添加多个键/值对，但是在构造形式中必须逐个添加属性。

#### 3.2 类型

对象是JavaScript的基础。在JavaScript中一共有六种主要类型

* string
* number
* boolean
* null
* undefined
* object

简单基本数据类型并不是对象。

null 有时会被当做一个对象类型，其实这是一个bug，null本身是基本类型。

常见的错误认为‘JavaScript中万物皆是对象’。

内置对象

JavaScript中海油一些对象子类型，通常被称为内置对象。有些内置对象的名字看起来和简单基础类型一样。

* String
* Number
* Boolean
* Object
* Function
* Array
* Date
* RegExp
* Error

#### 3.3 内容

之前我们提到过的，对象的内容是存储在对象内部，其实存储在对象容器内部的是这些属性的名称，它们就像指针（引用）一样，指向这些真正的存储位置。

值访问

```js
var myObj = {
  a: 2
};

myObj.a; // 2
myObj['a']; // 2
```

访问对象上的值可以通过.操作符和[]操作符。.操作符常被称为属性访问，[]操作符被称为键访问。

```js
var myObj = {};

myObj[true] = 'foo';
myObj[3] = 'bar';
myObj[myObj] = 'baz';

myObj['true']; // foo
myObj['3']; // bar
myObj['[object Object]']; 'baz'
```

##### 3.3.1 可计算属性名

```js
var prefix = 'foo';

var myObj = {
  [prefix + 'bar']: 'hello',
  [prefix + 'baz']: 'world'
};

myObj['foobar']; // hello
myObj['foobaz']; // world
```

##### 3.3.2 属性描述符

```js
var myObj = {
  a: 2
};

Object.getOwnPropertyDescriptor(myObj, 'a');
{
  value: 2,
    writable: true,
    enumerable: true,
    configurable: true
}
```

由上可见，一个对象属性对应的属性描述符，不仅仅是一个2.还包括另外三个特性：writable(可写的)、enumerable（可枚举的）、configurable（可配置）。

普通来说，创建普通属性时属性描述符会使用默认值。我们也可以使用Object.defineProperty(..)

```js
var myObj = {};

Object.defineProperty(myObj, 'a', {
  value: 2,
  writable: true,
  configurable: true,
  enumerable: true
});

myObj.a; // 2
```

这里使用defineProperty(..)给myObj添加了一个普通的属性并显式指定了一些特性。

1. Writable

   writable 决定是否可以修改属性的值

   ```js
   var myObj = {};

   Object.defineProperty(myObj, 'a', {
     value: 2,
     writable: false, // 不可写
     configurable: true,
     enumerable: true
   });

   myObj.a = 3;
   myObj.a; // 2
   ```

   如你所见，对于属性值的修改静默失败了。如果在严格模式下，这种方式会出错。

   ```js
   'use strict';

   var myObj = {};

   Object.defineProperty(myObj, 'a', {
     value: 2,
     writable: false, // 不可写
     configurable: true,
     enumerable: true
   });

   myObj.a = 3; // TypeError
   // TypeError 错误表示无法修改一个不可写的属性。
   ```

2. Configurable

   只要是属性可配置的，就可以用Object.defineProperty(..)方法来修改属性描述符：

   ```js
   var myObj = {
     a: 2
   };

   myObj.a = 3;
   myObj.a; // 3

   Object.defineProperty(myObj, 'a', {
     value: 4,
     writable: true,
     configurable: false,
     enumerable: true
   });

   myObj.a; // 4
   myObj.a = 5;
   myObj.a; // 5

   Object.defineProperty(myObj, 'a', {
     value: 6,
     writable: true,
     configurable: true,
     enumerable
   }); // TypeError
   ```

   最后一个defineProperty会报TypeError错误，configurable修改成false是单向操作，不可逆。除此之外，还禁止删除这个属性。

3. enumerable

这个属性控制对象是否可以被枚举。如果设置为false，那么这个属性就不会出现在枚举中。

##### 3.3.3 不可变性

1. 对象常量

   ```js
   var myObj = {};

   Object.defineProperty(myObj, 'FAVORITE_NUMBER', {
     value: 2,
     writable: false,
     configurable: false
   });
   ```

2. 禁止扩展

   如果你想禁止一个对象添加新属性并且保留已有属性，可以使用Object.preventExtensions(..);

   ```js
   var myObj = {
     a: 2
   };

   Object.preventExtensions(myObj);

   myObj.b = 3;
   myObj.b; // undefined
   ```

3. 密封

   Object.seal(..)会创建一个“密封”的对象，这个方法实际上是调用Object.preventExtensions(..)使得对象上的所有属性都被设置为configurable: false，这样的话，密封之后不仅不能添加新的属性，也不能重新配置或者任何现有属性。

4. 冻结

   Object.freeze(..)会创建一个冻结对象，这个方法实际调用的是Object.seal(..)并把所有的“数据访问”属性标志位writable: false，这样就无法修改他们的值。

   这个是应用在对象上的最高级别的不可变性，会禁止对于对象本身及其任意直接属性的修改

##### 3.3.4 [[Getter和Setter]]

```js
var myObj = {
  get a() {
    return 2;
  }
};

Object.defineProperty(
  myObj,
  "b",
  {
    get: function() {
      return this.a * 2;
    },
    enumerable: true
  }
);

myObj.a; // 2
myObj.b; // 4
```

属性访问的时候回自动调用一个隐匿的函数，它的返回值会被当做属性访问的返回值。

```js
var myObj = {
  get a() {
    return this._a_;
  },
  
  set a(val) {
    this._a_ = val * 2;
  }
};

myObj.a = 2;
myObj.a; // 4
```

##### 3.3.5 存在性

```js
var myObj = {
  a: 2
};

('a' in myObj); // true
('b' in myObj); // false

myObj.hasOwnProperty('a'); // true
myObj.hasOwnproperty('b'); // false
```

in操作符会检查是否在对象及其原型链上

hasOwnProperty(..)只会检查属性是否在myObj中，不会检查Prototype链

小结：

JavaScript中的对象有字面形式和构造形式。字面形式更常用，不过有时候构造形式可以提供更多选项。

属性的特性可以通过属性描述符来控制，比如writable和configurable。此外使用Object.preventExtensions(..)、Object.seal(..)和Object.freeze(..)来设置对象的不可变性级别。

### 原型

对于默认的[[GET]]操作来说，如果无法在对象本身无法找到需要的属性，就会继续访问对象的[[prototype]]链。

```js
var anotherObject = {
  a: 2
};

// 创建一个关联到anotherObject的对象
var myObject = Object.create(anotherObject);

myObject.a; // 2
```

myObject的prototype被关联到anotherObject上，a属性本身在myObject上是找不到的，但是可以沿着原型链一直往上查找，结果在anotherObject上找到a属性。

还有一个，在myObject上用for in遍历的时候，可以把原型链上的属性也遍历出来。

继承

```js
function Foo(name) {
  this.name = name;
}

Foo.prototype.myName = function() {
  return this.name;
};

function Bar(name, label) {
  Foo.call(this, name);
  this.label = label;
}

// 创建一个新的Bar.prototype对象关联到Foo.prototype
Bar.prototype = Object.create(Foo.prototype);

Bar.prototype.myLabel = function() {
  return this.label;
};

var a = new Bar('a', 'obj a');

a.myName(); // a
a.myLabel(); // obj a
```

Bar.prototype = Object.create(Foo.prototype); 这段代码会凭空创建一个新对象并把新对象的内部的[[prototype]]关联到指定的对象（Foo.prototype）。

注意：

// 和你想要的机制不一样

Bar.prototype = Foo.prototype;

// 基本上满足你的需求，但是可能会产生一些副作用

Bar.prototype = new Foo();

Bar.prototype = Foo.prototype;并不会创建一个关联到Bar.prototype的新对象，它只是让Bar.prototype直接饮用Foo.prototype对象。因此当你执行类似Bar.prototype.myLabel = ..赋值语句会直接修改Foo.prototype对象本身。

Bar.prototype = new Foo();的确会创建一个关联到Bar.prototype的新对象。如果函数Foo的有一些副作用（比如写日志，修改状态，给this添加属性等等），就会影响到Bar()的“后代”。

小结：

如果要访问对象中并不存在的一个属性，就会查找对象内部的[[Prototype]]关联的对象。这个关联关系实际上定义了一条“原型链”，在查找属性的时候会对其进行遍历。

所有普通对象都有内置的Object.prototype,指向原型链的顶端，如果在原型链中找不到指定的属性就会停止。toString()、valueOf()和其他的一些通用的功能都存在于Object.prototype对象上，所有的对象都可以使用它们。

使用new调用函数会把新新对象的.prototype属性关联到"其他对象"。带new的函数调用通常称为“构造函数调用”。

虽然这些JavaScript机制和传统面向类语言中的“类初始化”和“类继承”很相似，但是有一个核心区别，那就是不会进行复制，对象之间是通过内部的[[prototype]]链关联的。

### 行为委托

#### 6.1面向委托的设计

```js
function Foo(who) {
  this.me = who;
}

Foo.prototype.identify = function() {
  return 'i am ' + this.me;
};

function Bar(who) {
  Foo.call(this, who);
}

Bar.prototype = Object.create(Foo.prototype);

Bar.prototype.speak = function() {
  console.log('hello, ' + this.identify() + '.');
};

var b1 = new Bar('b1');
var b2 = new Bar('b2');

b1.speak(); // hello, i am b1
b2.speak(); // hello, i am b2
```

子类 Bar 继承了父类 Foo，然后生成了 b1 和 b2 两个实例。b1 委托了 Bar.prototype，后者委托Foo.prototype。

```js
Foo = {
  init: function(who) {
    this.me = who;
  },
  identify: function() {
    return 'i am ' + this.me;
  }
}
Bar = Object.create(Foo);

Bar.speak = function() {
  console.log('hello, ' + this.identify() + '.');
};

var b1 = Object.create(Bar);
b1.init('b1');
var b2 = Object.create(Bar);
b2.init('b2');

b1.speak(); // hello, i am b1
b2.speak(); // hello, i am b2
```

这段代码中我们同样利用 [[Prototype]] 把 b1 委托给 Bar 并把 Bar 委托给 Foo，和上一段 代码一模一样。我们仍然实现了三个对象之间的关联。

但是非常重要的一点是，这段代码简洁了许多，我们只是把对象关联起来，并不需要那些既复杂又令人困惑的模仿类的行为(构造函数、原型以及 new)。

小结：

在大部分的编程语言中，使用类和继承的设计模式仿佛成了真理，大部分的开发者理所当然地认为类是唯一的代码组织方式，但是在上面介绍的行为委托似乎提供了一个更加强大的设计模式。（不是靠复制来实现对象的赋值）

行为委托认为对象之间是兄弟关系，互相委托，而不是父类和子类的关系。JavaScript的[[prototype]]机制本身就是行为委托机制。这个可以使得代码设计更加简洁清晰。

你不知道的JavaScript上卷主要讲了两部分，一部分是作用域和闭包，第二部分是this和对象原型。总体看下来有种醍醐灌顶的感觉，解决了以往遇到的很多疑惑点，收获满满，干货多多，接下来继续去中卷中探索更多的知识。
