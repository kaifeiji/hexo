---
title: JavaScript的Promise
date: 2023-02-02 19:16:01
categories:
- Mastering JS
tags:
- Promise
---

原文：[Promises in JavaScript](https://masteringjs.io/tutorials/fundamentals/promise)

Promise是一个代表异步操作的对象，它是JavaScript的基础并发原语。本文讲解了相关知识。

<!-- more -->

JavaScript的[`Promise`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)代表了一个异步操作。`Promise`有几种方法可以实现以下操作：注册一个[回调函数](https://masteringjs.io/tutorials/fundamentals/callbacks)`callback`，在某个操作成功或失败后JavaScript运行时调用`callback`。

在以下例子中，[HTTP库`axios`](https://www.npmjs.com/package/axios)返回了一个`Promise`。可以使用[`then()`函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/then)注册一个回调函数，在请求完成时JavaScript会调用回调函数。

```javascript
const axios = require('axios');

// `axios.get()` 返回一个HTTP请求的Promise
const promise = axios.get('https://httpbin.org/get?answer=42');

// `then()` 函数可以注册一个回调函数
// HTTP请求完成后JavaScript会调用回调函数
promise.then(res => {
  res.data.query.answer; // '42'
});
```

## Promise是状态机

可以把`Promise`看作是有3个状态的[状态机](https://en.wikipedia.org/wiki/Finite-state_machine)：

* **Pending：**操作正在进行中。
* **Fulfilled**：操作成功完成。
* **Rejected**：操作发生错误。

![Promise状态](/post-images/promises-in-javascript-2023-02-02-19-17-10.png)

当`Promise`创建时，它的状态总是`pending`。一旦`Promise`变成`fulfilled`或`rejected`，那么它可以被认为是处理完成，不会再改变状态了。`Promise`的状态是一个私有属性：任何一个`Promise`，没有简单的方法可以判断它的当前状态。

当`Promise`处理完成时，JavaScript运行时会调用`.then()`注册的回调函数。`then()`函数接收2个参数：`onFulfilled`和`onRejected`，`Promise`状态是`fulfilled`调用`onFulfilled`，状态是`rejected`则调用`onRejected`，

```javascript
// 创建一个Promise，立刻变为`fulfilled`，返回数值42。
Create a promise that is immediately fulfilled with value 42.
const promise = Promise.resolve(42);

const onFulfilled = () => {};
const onRejected = () => {};

// `Promise`状态是`fulfilled`调用`onFulfilled`
// 状态是`rejected`则调用`onRejected`
promise.then(onFulfilled, onRejected);
```

## 返回值和错误

当`Promise`变为`fulfilled`，会返回一个相关的值。`Promise`的这个值也是一个私有属性，访问它的唯一途径就是在`.then()`函数中。

```javascript
// 创建一个Promise，立刻变为`fulfilled`，返回数值42。
const promise = Promise.resolve(42);

promise.then(value => {
  value; // 42
});
```

当`Promise`变为`rejected`，会返回一个相关的错误。`Promise`的这个错误也是一个私有属性。

```javascript
// 创建一个 Promise 立刻变为 `rejected` 并返回一个错误对象
const promise = Promise.reject(new Error('Oops!'));

promise.then(null, err => {
  err.message; // 'Oops!'
});
```

[从头写一个`Promise`库](https://thecodebarbarian.com/write-your-own-node-js-promise-library-from-scratch.html)能让你学到更多哦。

## Promise的constructor

`Promise`是JavaScript的内置类，这意味着你可以用`new Promise()`来继承它。

`Promise`的constructor接收1个参数：`executor`函数。`executor`函数接收2个参数：`resolve()`和`reject()`回调函数，JavaScript运行时负责将这两个参数传给用户。

```javascript
const promise = new Promise(function executor(resolve, reject) {
  // Fulfill the promise with value '42' after 100 ms.
  setTimeout(() => resolve(42), 100);
});

promise.then(value => {
  value; // 42
});
```

*async/await是JavaScript中并发的未来趋势。[“Mastering Async/Await”](http://asyncawait.net/)教你如何在几个小时内，用async/await构建前后端APP，来看看吧！*
