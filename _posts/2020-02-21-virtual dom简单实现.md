---
layout:     post
title:      "virtual dom 简单实现"
subtitle:   "抽丝剥茧"
date:       2020-02-21
author:     "franki"
header-img: "images/posts/virtual-dom/virtual-dom.png"
tags:
    - 前端开发
    - 源码分析
    - virtual dom
---

* 目录  
{:toc #markdown-toc}

从事前端开发工作以来，一直使用 React 作为首选框架，熟练使用没问题，然而却只限于用的地步，对于其核心精髓 **virtual dom**，也就是听过，知道一些概念性的理念，只限于此。早就有想法，进入其真实世界，抽丝剥茧，今天就好好尝试从零到一实现一个极简的 **virtual dom**。

先声明，这是一个极简的 virtual dom，可能与 react 使用的 virtual dom或者与 vue 使用的 virtual dom 抑或其他 virtual dom 都不同，这里只探讨 virtual dom 核心理念及其实现，工业级别的 virtual dom 是超级庞大且复杂的。因此，我们不必陷入这些 source code 中。下面，let's go!

首先明确二个概念：

* 虚拟 dom 是真实 dom 的一种表现形式
* 当我们在虚拟 tree 进行更改时，我们将获得一棵新的虚拟 tree，算法会去比较这两棵 tree（新tree和旧tree），发现差异并对真实 dom 进行必要的**细微**修改

## 表现dom tree

假设我有这样的一棵 dom tree，我们会将以某种方式在内存中存储 dom tree，我们可以通过一个 js 对象来实现。

```html
<ul class='list'>
  <li>item 1</li>
  <li>item 2</li>
</ul>
```

如何通过 js 来表现为

```js
{ type: 'ul', props: { 'class': 'list' }, children: [
  { type: 'li', props: {}, children: ['item 1'] },
  { type: 'li', props: {}, children: ['item 2'] }
] }
```

可以通过这样的方式

```js
function h(type, props, ...children) {
  return { type, props, children };
}
```

创建dom tree可以这样

```js
h('ul', { class: 'list' },
  h('li', {}, 'item 1'),
  h('li', {}, 'item 2'),
);
```

其实方面的过程可以更简单，记得我们的jsx是如何做的吗

如果你看过babel官方文档 [here](https://babeljs.io/docs/plugins/transform-react-jsx/), 你可以知道，babel 可以将以下代码编译。

```html
<ul className='list'>
  <li>item 1</li>
  <li>item 2</li>
</ul>
```

会变成这样

```js
React.createElement('ul', { className: 'list' },
  React.createElement('li', {}, 'item 1'),
  React.createElement('li', {}, 'item 2'),
);
```

通过上面说的，现在我们可以这样做

```js
/** @jsx h */
const a = (
  <ul className='list'>
    <li>item 1</li>
    <li>item 2</li>
  </ul>
);
```

透过 babel 可以转化为

```js
const a = (
  h('ul', { className: 'list' },
    h('li', {}, 'item 1'),
    h('li', {}, 'item 2'),
  );
);
```

当h函数执行时，可以得到

```js
const a = (
  { type: 'ul', props: { className: 'list' }, children: [
    { type: 'li', props: {}, children: ['item 1'] },
    { type: 'li', props: {}, children: ['item 2'] }
  ] }
);
```

## 应用到真实dom

在这之前，我们来明确一些术语。

* 用$开头的所有变量表示真实 dom 节点
* 虚拟 dom 用 node 表示
* 就像 React 一样，只有一个根节点，其他的节点都在内部

现在我们来写一个函数将 virtual dom 转化成 real dom

```js
function createElement(node) {
  if (typeof node === 'string') {
    return document.createTextNode(node);
  }
  const $el = document.createElement(node.type);
  node.children
    .map(createElement)
    .forEach($el.appendChild.bind($el));
  return $el;
}
```

## 处理变更

ok，现在我们已将可以将虚拟 dom 转化为真实 dom了，接下来，要思考如何来扩展虚拟 tree。因此，我们需要编写一个算法来比较两个虚拟 tree（新tree和旧tree），并对真实的 dom 进行必要的修改。

来看看这些改变

* 在某个地方没有旧节点，因此添加了节点，我们需要 appendChild(...)

![diff1](/images/posts/virtual-dom/diff1.png)

* 在某个地方没有新节点，因此该节点被删除了，我们需要 removeChild(...)

![diff2](/images/posts/virtual-dom/diff2.png)

* 在某个地方有一个不同的节点，因此节点发生了变化，我们需要 replaceChild(...)

  ![diff3](/images/posts/virtual-dom/diff3.png)

* 节点是相同的，因此需要更深入区分子节点

![diff4](/images/posts/virtual-dom/diff4.png)

接下来，我们会编写一个 updateElement 函数，用于处理以上的每一个情况，这个函数接收三个参数，一个是 $parent(虚拟dom真实节点元素的父节点)，还有 newNode 和 oldNode

### 没有旧节点

```js
function updateElement($parent, oldNode, newNode) {
  if (!oldNode) {
    $parent.appendChild(
      createElement(newNode)
    );
  }
}
```

### 没有新节点

在虚拟 tree 中当前位置没有节点，故需要从真实 dom 中移除，通过 $parent.removeChild(...)，传递真实 dom 元素引用，将其删除，但是现在没有？所以得通过节点在父元素的的位置，

通过 $parent.childNodes[index] 获得其引用，其中 index 是节点在父元素里面的位置。

```js
function updateElement($parent, oldNode, newNode, index = 0) {
  if (!oldNode) {
    $parent.appendChild(
      createElement(newNode)
    );
  } else if (!newNode) {
    $parent.removeChild(
      $parent.childNodes[index]
    );
  }
}
```

### 节点变更

首先，我们需要写一个函数来判断节点是否发生了变更

```js
function changed(node1, node2) {
  return typeof node1 !== typeof node2 ||
         typeof node1 === 'string' && node1 !== node2 ||
         node1.type !== node2.type
}
```

现在，我们有了当前节点的位置，可以很容易地替换它

```js
function updateElement($parent, newNode, oldNode, index = 0) {
  if (!oldNode) {
    $parent.appendChild(
      createElement(newNode)
    );
  } else if (!newNode) {
    $parent.removeChild(
      $parent.childNodes[index]
    );
  } else if (changed(newNode, oldNode)) {
    $parent.replaceChild(
      createElement(newNode),
      $parent.childNodes[index]
    );
  }
}
```

### 对比children

最后，我们需要对两个节点的每一个子节点调用 updateElement 进行循环递归遍历，逐个比较

编写代码前，需要考虑以下事情：

* 仅当是元素才进行比较（文本节点没有 children ）
* 我们将对当前节点的引用作为父元素传递
* 比较所有的子元素，即便得到的 undefined 也无所谓，可以通过函数去处理
* 索引是 children 数组里面的子节点的索引

```js
function updateElement($parent, newNode, oldNode, index = 0) {
  if (!oldNode) {
    $parent.appendChild(
      createElement(newNode)
    );
  } else if (!newNode) {
    $parent.removeChild(
      $parent.childNodes[index]
    );
  } else if (changed(newNode, oldNode)) {
    $parent.replaceChild(
      createElement(newNode),
      $parent.childNodes[index]
    );
  } else if ((oldNode.children && oldNode.children.length)
        || (newNode.children && newNode.children.length)) {
    const newLength = newNode.children.length;
    const oldLength = oldNode.children.length;
    for (let i = 0; i < newLength || i < oldLength; i++) {
      updateElement(
        $parent.childNodes[index],
        newNode.children[i],
        oldNode.children[i],
        i
      );
    }
  }
}
```

以上，我们已经实现初级版本的 virtual dom，可以看到以下效果

![res1](/images/posts/virtual-dom/res1.gif)

仔细想想，如果我们还要

* 设置 attribute 并去 diff 它们
* 处理事件-为我们的元素添加事件监听
* 获取真实 dom 节点的引用

下面一一来处理

### 设置props

设置属性是非常简单的一件事情

先定义一个setProp函数用于设置属性

```js
function setProp($target, name, value) {
  $target.setAttribute(name, value);
}
```

然后定义一个 setProps 函数来设置全部属性

```js
function setProps($target, props) {
  Object.keys(props).forEach(name => {
    setProp($target, name, props[name]);
  });
}
```

然后去到 createElement 函数，在创建元素的同时设置属性

```js
function createElement(node) {
  if (typeof node === 'string') {
    return document.createTextNode(node);
  }
  const $el = document.createElement(node.type);
  setProps($el, node.props);
  node.children
    .map(createElement)
    .forEach($el.appendChild.bind($el));
  return $el;
}
```

现在可以设置成

```html
<nav className='navbar light'>
  <ul></ul>
</nav>
```

还有这种情况

```html
<input type='checkbox' checked={false} />
```

需要使用

```js
function setBooleanProp($target, name, value) {
  if (value) {
    $target.setAttribute(name, value);
    $target[name] = true;
  } else {
    $target[name] = false;
  }
}
```

如果还有自定义的 prop，可以先不设置

```js
function isCustomProp(name) {
  return false;
}
```

现在 setProp 函数写成这样

```js
function setProp($target, name, value) {
  if (isCustomProp(name)) {
    return;
  } else if (name === 'className') {
    $target.setAttribute('class', value);
  } else if (typeof value === 'boolean') {
    setBooleanProp($target, name, value);
  } else {
    $target.setAttribute(name, value);
  }
}
```

### props变更

既然可以设置属性，当然也需要去移除属性

```js
function removeBooleanProp($target, name) {
  $target.removeAttribute(name);
  $target[name] = false;
}
function removeProp($target, name, value) {
  if (isCustomProp(name)) {
    return;
  } else if (name === 'className') {
    $target.removeAttribute('class');
  } else if (typeof value === 'boolean') {
    removeBooleanProp($target, name);
  } else {
    $target.removeAttribute(name);
  }
}
```

现在我们写一个 updateProp 去对比两个属性（旧属性和新属性），并根据结果修改 dom 节点，我们需要处理一下情况：

* 新节点中没有该名称的属性，需要移除它

  ![diff5](/images/posts/virtual-dom/diff5.png)

* 老节点中没有该名称的属性，需要设置它

  ![diff6](/images/posts/virtual-dom/diff6.png)

* 如果新老节点都存在该属性，那么需要深度对比，如果是它们不相等，那么就要用新节点的值去设置属性

  ![diff7](/images/posts/virtual-dom/diff7.png)

* 其他情况下都不需要做任何事情

定义一个 updateProp 去更新属性

```js
function updateProp($target, name, newVal, oldVal) {
  if (!newVal) {
    removeProp($target, name, oldVal);
  } else if (!oldVal || newVal !== oldVal) {
    setProp($target, name, newVal);
  }
}
```

定义一个函数去更新所有的属性

```js
function updateProps($target, newProps, oldProps = {}) {
  const props = Object.assign({}, newProps, oldProps);
  Object.keys(props).forEach(name => {
    updateProp($target, name, newProps[name], oldProps[name]);
  });
}
```

回到 updateElement 函数，把 diff prop 的部分加上

```js
function updateElement($parent, newNode, oldNode, index = 0) {
  if (!oldNode) {
    $parent.appendChild(
      createElement(newNode)
    );
  } else if (!newNode) {
    $parent.removeChild(
      $parent.childNodes[index]
    );
  } else if (changed(newNode, oldNode)) {
    $parent.replaceChild(
      createElement(newNode),
      $parent.childNodes[index]
    );
  } else if (newNode.type) {
    updateProps(
      $parent.childNodes[index],
      newNode.props,
      oldNode.props
    );
  } else if ((oldNode.children && oldNode.children.length)
        || (newNode.children && newNode.children.length)) {
    const newLength = newNode.children.length;
    const oldLength = oldNode.children.length;
    for (let i = 0; i < newLength || i < oldLength; i++) {
      updateElement(
        $parent.childNodes[index],
        newNode.children[i],
        oldNode.children[i],
        i
      );
    }
  }
}
```

### 处理事件

在 React 我们常见到

```html
<button onClick={() => alert('hi!')}></button>
```

我们通过

```js
function isEventProp(name) {
  return /^on/.test(name);
}
```

来判断是否是事件

用 extractEventName 来确定事件名

```js
function extractEventName(name) {
  return name.slice(2).toLowerCase();
}
```

上面的 isCustomProp 可以设置成这样

```js
function isCustomProp(name) {
  return isEventProp(name);
}
```

现在来设置事件监听

```js
function addEventListener($target, name, value) {
    if (isEventProp(name)) {
        $target[`${name.toLowerCase()}`] = value;
    }
}
```

移除事件监听是这样

```js
function removeEventListener($target, name) {
    if (isEventProp(name)) {
        $target[`${name.toLowerCase()}`] = null;
    }
}
```

在 setProp 函数中加入

```js
function setProp($target, name, value) {
  if (isCustomProp(name)) {
      addEventListener($target, name, value);
  }...
}
```

在 removeProp 函数中加入

```js
function removeProp($target, name, value) {
  if (isCustomProp(name)) {
    removeEventListener($target, name, value);
  }...
}
```

至此，virtual dom 的大部分工作，已经完毕，可以看到以下效果：

![virtual-dom](http://qiniu.sevenyuan.cn/virtual-diff.gif)

总结：

这里的 virtual dom 使用的比对算法比较简单直接，通过同层元素比对，得到更改后的虚拟dom tree，并应用到真实的dom上（最小修改原则）

当我们面对对于耳熟能详的东西，不能只停留在概念上，需要更进一步探索，而不是人云亦云，只有这样，才会知道为啥 react、 vue 这些框架能如此大行其道，通过学习，掌握了比对技巧和节点引用，对于自己以后的工作生活中受益匪浅。

以上完整的代码，可以前往

[realize-virtual-dom](https://github.com/NikFranki/realize-virtual-dom)

文章参考：

[how to write your virtual dom part one](https://medium.com/@deathmood/how-to-write-your-own-virtual-dom-ee74acc13060)

[how to write your virtual dom part two](https://medium.com/@deathmood/write-your-virtual-dom-2-props-events-a957608f5c76)
