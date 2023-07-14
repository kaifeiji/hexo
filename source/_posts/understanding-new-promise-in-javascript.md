---
title: 理解JavaScript中new Promise
date: 2023-02-19 21:34:50
categories:
- Mastering JS
hide: true
tags:
- Promise
- new
---

原文：[Understanding `new Promise` in JavaScript](https://masteringjs.io/tutorials/fundamentals/promise-new)

在使用`new Promise`时，调用的时Promise的constructor。本文介绍了Promise constructor的专业用法。

<!-- more -->

[Promise的constructor](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)接收一个参数，`executor`函数。JavaScript环境会执行这个函数，并传入2个参数：`resolve()`和`reject()`。

```javascript
function executor(resolve, reject) {
  typeof resolve; // 'function'
  typeof reject; // 'function'
}

new Promise(executor);
```

executor函数主要用于调用`resolve()`标记为fulfilled（成功），或用`reject()`标记为rejected（失败）。

```javascript
const success = new Promise(function executor(resolve) {
  resolve('OK');
});

const fail = new Promise(function executor(resolve, reject) {
  reject(new Error('Oops'));
});

const str = await success;
str; // 'OK'

const err = await fail.catch(err => err);
err.message; // Oops
```

用[`then()`函数](https://masteringjs.io/tutorials/fundamentals/then)可以注册一个事件监听器，在Promise变为fulfilld或rejected时执行函数。

```javascript
const success = new Promise(function executor(resolve) {
  setTimeout(() => resolve('OK'), 100);
});

const start = Date.now();

return success.then(function onFulfilled(str) {
  str; // 'OK'

  const elapsed = Date.now() - start;
  elapsed; // Approximately 100
});
```

## setTimeout中的Promise

一般开发过程中并不需要创建新的Promise，类似[axios](http://npmjs.com/package/axios)和[Mongoose](https://www.npmjs.com/package/mongoose)的库通常会在内部生成Promise然后作为结果返回，可以直接用then()函数或await进行处理。

然而，并非所有的API都支持Promise。例如，[`setTimeout()`函数](https://www.w3schools.com/jsref/met_win_settimeout.asp)只能接收回调函数。要创建一个100毫秒后resolve的Promise，要用`new Promise`包住一个`setTimeout()`：

```javascript
async function test() {
  // Pause the async function for 100ms
  await new Promise(resolve => setTimeout(resolve, 100));

  return 'OK';
}

const start = Date.now();
await test();
const elapsed = Date.now() - start;
elapsed; // 大约 100 毫秒
```

## Node风格的回调函数

一些Node.js的异步API，比如`fs.readFile()`，返回的不是Promise。也需要用new Promise包住`fs.readFile()`，这样才能使用async/await。

一定要记得错误处理！[Node风格的回调函数](https://masteringjs.io/tutorials/fundamentals/callbacks#node-style-callbacks)接收2个参数：一个`error`和一个`result`。如果`error`是[非空的](https://masteringjs.io/tutorials/fundamentals/falsy#nullish-values)，就需要reject这个Promise。

```javascript
const fs = require('fs');

const p = new Promise(function executor(resolve, reject) {
  fs.readFile('./package.json', (error, result) => {
    if (error != null) {
      // 注意这是提前返回！
      return reject(error);
    }

    resolve(result);
  });
});

const pkg = JSON.parse(await p);
pkg.name; // 'masteringjs.io'
```

## 异步执行函数

一个常见的错误是把执行函数作为[异步函数](https://thecodebarbarian.com/async-functions-in-javascript.html)处理。

```javascript
const p = new Promise(async function executor(resolve, reject) {
  await new Promise(resolve => setTimeout(resolve, 100));
  resolve('OK');
});

const str = await p;
```

以上代码可以正常运行，但是它创建了一个不必要的Promise（记住，异步函数总是返回一个Promise！），看起来有点多余。因为async函数总是返回一个Promise，可以用一个原生的异步函数调用替换异步执行函数：

```javascript
async function test() {
  await new Promise(resolve => setTimeout(resolve, 100));
  return 'OK';
}

const p = test();
```

关键的问题是，永远不需要写一个异步的执行函数。

*async/await是JavaScript中并发的未来趋势。[“Mastering Async/Await”](http://asyncawait.net/)教你如何在几个小时内，用async/await构建前后端APP，来看看吧！*
