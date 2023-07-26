---
title: JavaScript的Promise链
date: 2023-07-24 14:38:44
categories:
- Mastering JS
hide: true
tags:
- Promise
---

原文：[JavaScript Promise Chaining](https://masteringjs.io/tutorials/fundamentals/promise-chaining)

Promise链式调用是一种很强大的模式，最大的好处是可以用一个`catch()`处理所有的错误。

<!-- more -->

Promise链，比[回调函数](https://masteringjs.io/tutorials/fundamentals/callbacks#node-style-callbacks)有更好的语义结构。关键点在于[Promise的`then()`函数](https://masteringjs.io/tutorials/fundamentals/then)返回另一个Promise，因此可以链式调用`.then()`，实现按一定顺序执行异步操作。

```javascript
const start = Date.now();
return Promise.resolve().
  then(() => new Promise(resolve => setTimeout(resolve, 50))).
  then(() => new Promise(resolve => setTimeout(resolve, 50))).
  then(v => {
    console.log(Date.now() - start); // 过大约100ms
  });
```

## 返回值

`then()`函数的第一个参数是`onFulfilled()`，JavaScript会在Promise状态变为fulfilled时，调用该函数，传入该函数的第一个参数就时resolve返回的值。

如果`onFulfilled()`函数返回一个Promise `q`，调用Promise的`then()`会[沿用](https://promisesaplus.com/#point-49)`q`的状态，其返回值具有和`q`相同的resolve返回值。这就是Promise链的工作原理。

```javascript
return Promise.resolve(1).
  // 如果`onFulfilled()`返回一个Promise，JavaScript会调用
  // 下一个`onFulfilled()`，同时将前一个resolve的值传递到
  // 下一个`onFulfilled()`
  then(v => new Promise(resolve => setTimeout(() => resolve(v + 1), 10))).
  then(v => new Promise(resolve => setTimeout(() => resolve(v + 1), 10))).
  then(v => new Promise(resolve => setTimeout(() => resolve(v + 1), 10))).
  // 如果`onFulfilled()`返回值的不是一个Promise
  // JavaScript会将返回值包装成Promise，并传递下去
  then(v => v + 1).
  then(v => {
    console.log(v); // 5
  });
```

## 错误处理

Promise链对错误进行了合并处理。你只需要在Promise链的最后调用一次[`.catch()`函数](https://masteringjs.io/tutorials/fundamentals/catch)，就可以处理Promise链中任何位置发生的错误。

```javascript
Promise.resolve(1).
  then(v => v + 1).
  // 链中间的异步错误直接进入`catch()`
  then(() => Promise.reject(new Error('Oops'))).
  then(v => v + 1).
  catch(err => {
    err.message; // 'Oops'
  });

Promise.resolve(1).
  then(v => v + 1).
  // 链中间的同步错误也直接进入`catch()`
  then(() => { throw new Error('Oops'); }).
  then(v => v + 1).
  catch(err => {
    err.message; // 'Oops'
  });
```

## 总结

Promise链的结构，包含一系列的`.then()`调用，每个都带有`onFulfilled()`参数，以及结尾的`.catch()`。JavaScript按顺序执行`.then()`的回调函数，在`onFulfilled()`函数发生错误时，直接进入`.catch()`。

![Promise链执行逻辑](/post-images/javascript-promise-chaining-2023-07-23-14-43-23.png)


*async/await是JavaScript中并发的未来趋势。[“Mastering Async/Await”](http://asyncawait.net/)教你如何在几个小时内，用async/await构建前后端APP，来看看吧！*