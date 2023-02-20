---
title: 理解JavaScript中async/await
date: 2023-02-20 13:04:26
categories:
- Mastering JS
tags:
- async
- await
---

原文：[Understanding Async/Await in JavaScript](https://masteringjs.io/tutorials/fundamentals/async-await)

async/await可以用同步的方式写异步代码，没有回调函数或Promise链，可以在`for`循环和`if`语句中使用。

<!-- more -->

async/await可以用同步的方式写异步代码，在异步函数中，可以用if语句、for循环和try/catch。

## async

`async`关键字表示一个函数是[异步函数](https://thecodebarbarian.com/async-functions-in-javascript.html)。以下示例中，`test()`是异步函数。

```javascript
async function test() {
  return 42;
}
```

也可以定义一个异步的箭头函数：

```javascript
const test = async () => 42;
```

## await

async函数的特殊之处在于，可以在函数中使用`await`关键字，如果`await`一个Promise，会暂停执行该函数，直到Promise变为[fulfilled或rejected](https://masteringjs.io/tutorials/fundamentals/promise#promises-as-state-machines)。`await`还会解开Promise的包装，取得Promise变为fulfilled的值。

```javascript
async function test() {
  // `await`解包了Promise的值
  const val = await Promise.resolve(42);
  val; // 42
}

test();
```

在以上示例中，[`Promise.resolve()`函数](https://masteringjs.io/tutorials/fundamentals/promise-resolve)代表这个Promise会立刻变为fulfilled。在以下示例中，`await`暂停了`test()`函数100毫秒：

```javascript
async function test() {
  const start = Date.now();
  await new Promise(resolve => setTimeout(resolve, 100));
  const elapsed = Date.now() - start;
  elapsed; // about 100
}
```

`await`是JavaScript的一个普通的关键字，可以在`if`语句、`for`循环和`try/catch`中使用。

```javascript
async function asyncEvenNumbers() {
  const nums = [];
  for (let i = 1; i <= 10; ++i) {
    if (i % 2 === 0) {
      const v = await Promise.resolve(i);
      nums.push(v);
    }
  }

  nums; // [2, 4, 6, 8, 10]
}
```

## 返回值

async函数的另一个特殊之处在于，它总是返回一个Promise。即使在async函数中返回的是一个原始类型的值，JavaScript会将它包装为Promise。

```javascript
async function test() {
  return 42;
}

const p = test();
p instanceof Promise; // true
p.then(v => {
  v; // 42
});
```

也就是说，可以对async函数使用`await`：

```javascript
async function test() {
  return 42;
}

async function main() {
  const val = await test();
  val; // 42
}
```

## 错误处理

[async/await的错误处理](http://thecodebarbarian.com/async-await-error-handling-in-javascript.html)是一个复杂的话题。但是，大体上有两种错误处理的模式。

在`await`一个Promise时，Promise变为rejected，`await`会抛出一个错误，可以用`try/catch`捕获：

```javascript
async function test() {
  try {
    await Promise.reject(new Error('Oops'));
  } catch (err) {
    err.message; // Oops
  }
}
```

也可以用[`Promise#catch()`函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch)解包Promise的错误：

```javascript
async function test() {
  const promise = Promise.reject(new Error('Oops'));

  // Unwrap the promise's error
  const err = await promise.catch(err => err);
  err.message; // 'Oops'
}
```

*async/await是JavaScript中并发的未来趋势。[“Mastering Async/Await”](http://asyncawait.net/)教你如何在几个小时内，用async/await构建前后端APP，来看看吧！*