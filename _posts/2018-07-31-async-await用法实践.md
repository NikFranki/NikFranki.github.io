---
layout:     post
title:      "async-await用法实践"
subtitle:   ""
date:       2018-07-31
author:     "franki"
header-img: "images/post-bg-js-module.jpg"
tags:
    - 前端开发
    - ES7
    - JavaScript
---

* 目录  
{:toc #markdown-toc}

解决回调难题，优化promise

## 直接进入主题

```code
async awiat 用于解决回调地狱的问题，进一步优化promise而创造出来的
async 用于声明是一个异步的函数，主要是用于将函数结果封装未promise，如Promise.resolve(res),若res是个直接值
则封装为promise，若是undefined，则直接就返回了；
await 主要作用是等待promise的返回，主要是等待promise resolve,优点是可以想同步代码一样写异步代码
```

### eg1 直接声明一个异步函数

```js
const testAsync = async () =>{
    return 'hello async';
}
const result = testAsync();
console.log(result); // 返回一个promise
```

### eg2 对比await处理普通结果和promise的结果的区别

```js
const getSomething = () => {
    return "something";
}

const testAsyncy1 = () => {
    return Promise.resolve('hello async');
}

const test = async () => {
    const v1 = await getSomething(); // 'something'
    const v2 = await testAsyncy1(); // 'hello async'
    console.log(v1, v2); // 'something' 'hello async'
}

test();
```

### eg3 对比原生promise解决异步，和async await解决异步的区别

```js
const takeLongTime = () => {
    return new Promise(resolve => {
        setTimeout(() => resolve('long_time_value'), 1000);
    });
}

takeLongTime().then(v => console.log('got', v));

const test1 = async () => {
    const v = await takeLongTime(); // 'long_time_value'
    console.log(v); // 'long_time_value'
}

test1();
```

### eg4 原生promise async await处理then链的区别

```js
const takeLongTime1 = (n) => {
    return new Promise(resolve => {
        setTimeout(() => resolve(n + 200), n)
    });
}

const step1 = (n) => {
    console.log(`step1 with ${n}`);
    return takeLongTime1(n);
}

const step2 = (n) => {
    console.log(`step2 with ${n}`);
    return takeLongTime1(n);
}

const step3 = (n) => {
    console.log(`step3 with ${n}`);
    return takeLongTime1(n);
}

const doIt = () => {
    console.log('doIt');
    const time1 = 300;
    step1(time1)
        .then(time2 => step2(time2))
        .then(time3 => step3(time3))
        .then(res => {
            console.log(`res is ${res}`);
            console.timeEnd("doIt");
        })
    // step1 with 300
    // step2 with 500
    // step3 with 700
    // res is 900
}

doIt();

const doIt1 = async () => {
    console.log("doIt");
    const time1 = 300;
    const time2 = await step1(time1);
    const time3 = await step2(time2);
    const res = await step3(time3);
    console.log(`res is ${res}`);
    console.timeEnd("doIt");
    // step1 with 300
    // step2 with 500
    // step3 with 700
    // res is 900
}

doIt1();
```

### eg5 炫酷处理

```js
const takeLongTime = (n) => {
    return new Promise(resolve => setTimeout(resolve(n + 200), n));
}

const step1 = (n) => {
    console.log(`step1 with ${n}`);
    return takeLongTime(n);
}

const step2 = (m, n) => {
    console.log(`step2 with ${m} and ${n}`);
    return takeLongTime(m + n);
}

const step3 = (m, n, k) => {
    console.log(`step3 with ${m} and ${n} and ${k}`);
    return takeLongTime(m + n +k);
}

const test = async () => {
    console.log("doIt");
    const time1 = 300;
    const time2 = await step1(time1);
    const time3 = await step2(time1, time2);
    const res = await step3(time1, time2, time3);
    console.log(`res is ${res}`);
    console.timeEnd("doIt");
    // step1 with 300
    // step2 with 300 500
    // step3 with 300 500 1000
    // res is 2000
}

test();
```
