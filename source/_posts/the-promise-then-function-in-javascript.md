---
title: JavaScript中Promise的then()函数
date: 2023-02-03 13:19:02
categories:
- Mastering JS
hide: true
tags:
- Promise.then
---

原文：[The Promise then() Function in JavaScript](https://masteringjs.io/tutorials/fundamentals/then)

`then()`函数是JavaScript中Promise交互的主要方式，是Promise链式语法的一部分。

<!-- more -->

JavaScript的[`Promise`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)代表了一个异步操作。可以认为`Promise`是一个还未被计算的值的占位符。然而，没办法直接从`Promise`获取到这个值——需要调用`then()`函数注册一个[回调函数](https://masteringjs.io/tutorials/fundamentals/callbacks)，当值被计算完成后JavaScript会调用这个回调函数。

```javascript
// 创建一个Promise，立刻变为`fulfilled`，返回数值42。
const promise = Promise.resolve(42);

promise.then(value => {
  value; // 42
});
```

## then()函数的参数

`then()`函数接收2个回调函数作为参数：

* `onFulfilled()`：底层的异步操作成功后调用。
* `onRejected()`：底层的异步操作失败后调用。

回忆一下，[`Promise`是3个状态的状态机](https://masteringjs.io/tutorials/fundamentals/promise#promises-as-state-machines)：

* **Pending：**操作正在进行中。
* **Fulfilled**：操作成功完成。
* **Rejected**：操作发生错误。

![](/post-images/promises-in-javascript-2023-02-02-19-17-10.png)

`Promise`总是以`pending`状态作为起始。如果`Promise`变成`fulfilled`状态，JavaScript调用`onFulfilled()`函数。如果在Promise已经是`fulfilled`状态，再调用`then()`，JavaScript会立即调用`onFulfilled()`。

```javascript
const promise = new Promise(function executor(resolve, reject) {
  // 100毫秒后Promise变为fulfilled，并返回值42
  setTimeout(() => resolve(42), 100);
});

promise.then(value => {
  value; // 42
});
```

如果`Promise`变成`rejected`状态，或者`Promise`已经是`rejected`状态时调用`then()`，JavaScript会调用`onFulfilled()`。

```javascript
// 创建一个 Promise 立刻变为 `rejected` 并返回一个错误对象
const promise = Promise.reject(new Error('Oops!'));

promise.then(null, err => {
  err.message; // 'Oops!'
});
```

`onFulfilled()`和`onRejected()`都是可选参数。如果像以上示例一样`onFulfilled()`是null，JavaScript在`Promise`变为`fulfilled`时不会做任何事。如果`Promise`状态是`rejected`，调用`.then()`时不带`onRejected()`函数，会导致[未捕获的错误](https://thecodebarbarian.com/unhandled-promise-rejections-in-node.js.html)。

## then()和catch()

[`Promise#catch()`函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch)是`.then()`一个方便的简写，`.catch(onRejected)`相当于`.then(null, onRejected)`的语法糖。

```javascript
// 创建一个 Promise 立刻变为 `rejected` 并返回一个错误对象
const promise = Promise.reject(new Error('Oops!'));

// 等价于 `.then(null, onRejected)`
promise.catch(function onRejected() {
  err.message; // 'Oops!'
});
```

虽然`.catch()`在原理上不复杂，但`catch`这个名字很有用，因为可以把`Promise`的`.catch()`类比`try/catch`中的`catch`，当你在浏览包含`Promise`的代码时，`.catch()`可以很容易分辨错误处理代码。

## 链式操作

说到`Promise`的实用性，`Promise`的链式操作功不可没。`then()`函数返回一个`Promise` `p`，如果传入的`onFulfilled()`函数返回一个`Promise` `q`，`p`会沿用`q`的状态。

```javascript
// 创建一个 Promise 立刻变为 `rejected` 并返回一个错误对象
const promise = Promise.reject(new Error('Oops!'));

// 等价于 `.then(null, onRejected)`
promise.catch(function onRejected() {
  err.message; // 'Oops!'
});
```

由于`Promise`的链式特性，不需要在每个`then()`中用捕获错误。在`Promise`链的结尾加一个`catch()`，链中的任何错误，都会穿过`Promise`链，直到被`catch()`捕获错误。

```javascript
Promise.resolve(1).
  then(() => Promise.resolve(2)).
  then(() => Promise.reject(new Error('Oops!'))).
  then(() => console.log('This will not print')).
  catch(function errorHandler(err) {
    err.message; // 'Oops!'
  });
```

*async/await是JavaScript中并发的未来趋势。[“Mastering Async/Await”](http://asyncawait.net/)教你如何在几个小时内，用async/await构建前后端APP，来看看吧！*