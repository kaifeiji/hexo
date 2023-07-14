---
title: JavaScript创建Promise
date: 2023-03-02 09:33:10
categories:
- Mastering JS
hide: true
tags:
- Promise
---

原文：[JavaScript Create Promise](https://masteringjs.io/tutorials/fundamentals/promise-create)

本文简述JavaScript创建Promise的几种方式。

<!-- more -->

总的来说，JavaScript有4种方式创建Promise：

* [Promise构造器](https://masteringjs.io/tutorials/fundamentals/promise-new)
* 静态函数[`Promise.resolve()`](https://masteringjs.io/tutorials/fundamentals/promise-resolve)和[`Promise.reject()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/reject)
* Promise链中的[`then()`函数](https://masteringjs.io/tutorials/fundamentals/then)和[`catch()`函数](https://masteringjs.io/tutorials/fundamentals/catch)
* 调用[`async`函数](https://thecodebarbarian.com/async-functions-in-javascript.html)

## Promise构造器

[Promise的构造器](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)只接收一个参数——`executor`函数。当调用`new Promise(executor)`时，JavaScript会立即执行`executor`函数，传入2个参数：`resolve()`和`reject()`。

```javascript
function executor(resolve, reject) {
  typeof resolve; // 'function'
  typeof reject; // 'function'
}

new Promise(executor);
```

`executor()`函数的作用，可以调用`resolve()`将Promise标记为[fulfilled](https://masteringjs.io/tutorials/fundamentals/promise#promises-as-state-machines)（成功），或调用`reject()`标记为rejected（失败）。

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

## 静态函数

`Promise.resolve()`函数可以创建一个Promise，随后立即变为fulfilled。

```javascript
const p = Promise.resolve(42);
p.then(v => {
  v; // 42
});
```

可以认为`Promise.resolve(v)`是`new Promise(resolve => resolve(v))`的简写。

类似的，`Promise.reject()`函数可以创建一个立即标记为rejected的Promise。

```javascript
const p = Promise.reject(new Error('Oops!'));
p.catch(err => {
  err.message; // 'Oops!'
});
```

用`Promise.reject()`时要小心：如果没有在其后加上`.catch()`，会发生“[未处理的Promise异常](https://thecodebarbarian.com/unhandled-promise-rejections-in-node.js.html)”。

## then()和catch()

在调用`.then()`或`.catch()`时，JavaScript会创建一个新的Promise。

```javascript
const p = Promise.resolve('Hello');

const p2 = p.then(str => `${str} World`);

p2 instanceof Promise; // true
p2 === p; // false
```

## async函数

在调用`async`函数时，JavaScript会返回一个新的Promise。无论`async`函数中返回的是什么，JavaScript都会返回一个Promise，一定要在`async`函数调用时加上`await`！

```javascript
async function test() {
  return 42;
}

test() instanceof Promise; // true
```

## 不立即执行

JavaScript的[Promise](https://masteringjs.io/tutorials/fundamentals/promise)在创建时会立即执行`executor`函数，从这个角度来看，Promise是“[热启动](https://staltz.com/cold-and-hot-callbacks.html)”的。

如果你需要一个“冷启动”的Promise，在创建时不执行，直到用`await`时才执行，那么你应该使用`async`函数。每次调用一个`async`函数都会返回一个新的Promise。

```javascript
async function getAnswer() {
  return 42;
}

const p1 = getAnswer();
p1 instanceof Promise; // true

const p2 = getAnswer();
p2 instanceof Promise; // true
p2 === p1; // false
```

另一种常见方式是[延迟模式](https://developer.mozilla.org/en-US/docs/Mozilla/JavaScript_code_modules/Promise.jsm/Deferred)，可以创建一个Promise，但是在`executor`函数之外调用`resolve()`和`reject()`函数。

```javascript
Promise.deferred = function() {
  let resolve = null;
  let reject = null;
  const p = new Promise((_resolve, _reject) => {
    resolve = _resolve;
    reject = _reject;
  });
  return Object.assign(p, { resolve, reject });
};

const p = Promise.deferred();

p.then(v => {
  v; // 42
});

p.resolve(42);
```

然而[延迟模式是一种反模式](https://github.com/petkaantonov/bluebird/wiki/Promise-anti-patterns#the-deferred-anti-pattern)，因为`executor`函数之外发生的同步错误，不会把Promise标记为rejected！

```javascript
// JavaScript会捕获Promise的executor中发生的任何错误
// 并将Promise标记为rejected
const p1 = new Promise(() => { throw new Error('Oops!'); });
p1.catch(err => {
  err.message; // 'Oops!'
});

// 用延迟模式，在executor外部你必须自己处理错误
// 如果你忘记处理错误，Promise会像p2一样永远保持pending状态
const p2 = Promise.deferred();
throw new Error('Oops!');
```
