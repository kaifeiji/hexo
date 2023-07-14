---
title: JavaScript中Promise的catch函数
date: 2023-03-01 10:38:23
categories:
- Mastering JS
hide: true
tags:
- catch
---

原文：[The Promise `catch()` Function in JavaScript](https://masteringjs.io/tutorials/fundamentals/catch)

Promise的catch函数是一个很方便的语法糖，用于处理Promise中的错误。

<!-- more -->

[JavaScript的Promise](https://masteringjs.io/tutorials/fundamentals/promise)是一个代表异步操作的对象。Promise就像是一个还未计算完成的值的占位符，一旦异步操作失败，JavaScript会将[Promise置为rejected状态](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/reject)。`catch()`函数会在Promise变为rejected时进行调用。

```javascript
const p = Promise.reject(new Error('Oops!'));

p.catch(err => {
  err.message; // 'Oops!'
});
```

## Promise链式操作

`.catch()`的一个主要好处是，可以捕获[Promise链](https://masteringjs.io/tutorials/fundamentals/then#chaining)中任意一环发生的错误。

```javascript
const p = Promise.resolve('Na');

return p.
  then(str => str + ' Na').
  then(str => str + ' Na').
  then(str => str + ' Na').
  then(() => { throw new Error('Batman!') }).
  then(() => console.log('Will not print')).
  catch(err => {
    err.message; // 'Batman!'
  });
```

也就是说可以在一个Promise链的最后加上`catch()`，来处理整条链上的错误。

## 重新抛出错误

与`try/catch`类似，在`.catch()`中可以重新抛出错误。

```javascript
const p = Promise.reject(new Error('Oops!'));

return p.
  catch(err => { throw err; }). // 重新抛出错误
  catch(err => {
    err.message; // 'Oops!'
  });
```

正因如此，在`catch()`中处理错误时，要特别小心未捕获的错误。如果传入`.catch()`的函数抛出错误，而没有后续的`.catch()`，会导致“[未处理的Promise错误](https://thecodebarbarian.com/unhandled-promise-rejections-in-node.js.html)”。

## 拆包错误

如果在`.catch()`中返回一个值，那么`await`或`then()`会“拆包”这个值。

```javascript
const p = Promise.reject(new Error('Oops!'));

const answer = await p.catch(() => 42);
answer; // 42

return p.
  catch(() => 42).
  then(answer => {
    answer; // 42
  });
```

换言之，[在JavaScript中可以用像Golang的语法处理异步错误](http://thecodebarbarian.com/async-await-error-handling-in-javascript.html#golang-in-js)。

```javascript
const p = Promise.reject(new Error('Oops!'));

const err = await p.catch(err => err);
err.message; // 'Oops!'
```

## 与then()比较

`catch()`函数只是基于[Promise的`then()`函数](https://masteringjs.io/tutorials/fundamentals/then)的语法糖。回忆一下`then()`的2个参数：

* `onFulfilled()`: JavaScript会在底层异步操作成功后调用
* `onRejected()`: JavaScript会在底层异步操作失败后调用

`.catch(fn)`相当于`.then(null, fn)`，以下是`catch()`的最简单的polyfill：

```javascript
Promise.prototype.catch = function(onRejected) {
  return this.then(null, onRejected);
};
```

其实用`.then()`也能处理Promise的错误：

```javascript
const p = Promise.reject(new Error('Oops!'));

return p.then(() => {}, function onRejected(err) {
  err.message; // 'Oops!'
});
```
