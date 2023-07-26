---
title: JavaScript中Promise的reject
date: 2023-07-25 15:02:43
categories:
- Mastering JS
hide: true
tags:
- Promise
---

原文：[Reject a Promise in JavaScript](https://masteringjs.io/tutorials/fundamentals/promise-reject)

在Promise中出现了错误，可以用reject将其标记为rejected状态。

<!-- more -->

[`Promise.reject()`函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/reject)以最简明的方式，将发生已知错误的[Promise标记为rejected](https://masteringjs.io/tutorials/fundamentals/promise#promises-as-state-machines)。在这之后还应该用`.catch()`处理错误。

```javascript
const p = Promise.reject(new Error('Oops!'));

return p.catch(err => {
  err.message; // 'Oops!'
});
```

## Promise构造器

当你使用`new`创建一个Promise时，其实是调用了[Promise的构造器](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)。Promise的构造器只接收一个参数——`executor`函数，Promise随后会执行`executor`函数，并传入2个参数：`resolve()`和`reject()`。

```javascript
function executor(resolve, reject) {
  typeof resolve; // 'function'
  typeof reject; // 'function'
}

new Promise(executor);
```

在executor函数中，要将Promise标记为rejected，可以调用`reject()`，并传入[Error对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error)。

```javascript
const p = new Promise((resolve, reject) => {
  reject(new Error('Oops!'));
});

return p.catch(err => {
  err.message; // 'Oops!'
});
```

## reject非Error值

reject并非只支持Error对象，也可以reject其他任意值。

```javascript
const p = Promise.reject(42);

return p.catch(err => {
  err; // 42
});
```

但是，许多库和框架假定Promise总是reject一个错误对象，所以在调用`Promise.reject()`传入其他值的时候，要特别小心。

*async/await是JavaScript中并发的未来趋势。[“Mastering Async/Await”](http://asyncawait.net/)教你如何在几个小时内，用async/await构建前后端APP，来看看吧！*