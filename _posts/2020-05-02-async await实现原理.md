---
layout:     post
title:      "async await实现原理"
subtitle:   ""
date:       2020-05-02
author:     "franki"
header-img: "images/posts/async-await/async-await.png"
catalog: true
tags:
    - async await
---

async await 原理

接上篇的generator，async await在generator基础上，继续推进异步使用的历史。

先说结论，async await的出现使得异步处理变得如鱼得水，写异步代码就像写同步代码一样简单，真的很感谢大佬们的智慧，不断创造新轮子，让我这种搬运工不断减少工作量，既然能力未到大佬们的位置，只能去观摩大佬们的实现，进入其里面一窥究竟。

```js
function fetchGg() {
  const res = fetch('google.com');
  return res;
}

function *start() {
  const res = yield fetchGg();
  return res;
}

function exceutor() {
  return spawn(start);
}

function spawn(genF) {
  return new Promise(function(resolve, reject) {
    const gen = genF();
    function step(nextF) {
      let next;
      try {
        next = nextF();
      } catch(e) {
        return reject(e);
      }
      if (next.done) {
          return resolve(next.value);
      }
      Promise.resolve(next.value).then(function(v) {
        step(function() {
          return gen.next(v);
        });
      }, function(e) {
        step(function() {
          return gen.throw(e);
        });
      });
    }
    step(function() {
      return gen.next();
    });
  });
}

const result = exceutor();
result.then(res => console.log(res));
```

看下babel是如何转化async await的

![babel_async-await](/images/posts/async-await/babel_async-await.png)

看到了吗？async await最终被转化为了generator，通过promise的形式传递生成器对象，直至生成器对象的next方法全部执行完毕，把最终的value返回出来。

小结：
async await的实现其实很简单，里面generator的特性，结合Promise，不断遍历递归generator对象，达到获取最后函数执行的值。
