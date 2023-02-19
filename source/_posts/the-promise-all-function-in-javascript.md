---
title: JavaScript的Promise.all()函数
date: 2023-02-17 22:55:53
categories:
- Mastering JS
tags:
- Promise.all
---

原文：[The `Promise.all()` Function in JavaScript](https://masteringjs.io/tutorials/fundamentals/promise-all)

`Promise.all()`函数可以并行执行多个Promise，然后收集执行结果。

<!-- more -->

[`Promise.all()`函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all)可以把一个Promise数组转换成单个Promise，当原数组中所有Promise[变为fulfilled](https://masteringjs.io/tutorials/fundamentals/promise#promises-as-state-machines)，它才变为fulfilled。

以下例子使用`Promise.all()`包住一个Promise数组：

```javascript
// `p1`马上fulfilled，返回值`1`
const p1 = Promise.resolve(1);
// `p2`100毫秒后fulfilled，返回值2
const p2 = new Promise(resolve => setTimeout(() => resolve(2), 100));

const pAll = Promise.all([p1, p2]);
pAll instanceof Promise; // true

const arr = await pAll;

Array.isArray(arr); // true
arr[0]; // 1
arr[1]; // 2
```

## 异步函数的并行执行

当使用[异步函数](https://thecodebarbarian.com/async-functions-in-javascript.html)时，`Promise.all()`可以并行的执行代码。将异步函数调用的数组传入`Promise.all()`，JavaScript会并行的执行异步函数。

假设有两个异步函数`getName()`和`getAge()`，以下展示了如何用`Promise.all()`并行的执行：

```javascript
async function getName() {
  await new Promise(resolve => setTimeout(resolve, 200));

  return 'Jean-Luc Picard';
}

async function getAge() {
  await new Promise(resolve => setTimeout(resolve, 200));

  return 59;
}

const start = Date.now();
const [name, age] = await Promise.all([getName(), getAge()]);
const end = Date.now();

name; // 'Jean-Luc Picard'
age; // 59

end - start; // Approximately 200
```

## 错误处理

如果其中一个Promise变为rejected，`Promise.all()`立刻变为rejected，抛出相同的错误。

```javascript
const success = new Promise(resolve => setTimeout(() => resolve('OK'), 100));
const fail = new Promise((resolve, reject) => {
  setTimeout(() => reject(new Error('Oops')), 100);
});

try {
  await Promise.all([success, fail]);
} catch (err) {
  err.message; // 'Oops'
}
```

注意，Promise是无法取消的，即使其中一个抛出错误，其他每个Promise仍然会继续执行。如果`Promise.all()`执行一个异步函数数组，其中一个异步函数抛出错误，`Promise.all()`会立即变为rejected，并返回错误。但其他的函数会继续执行下去。

```javascript
let finished = false;

const success = async function() {
  await new Promise(resolve => setTimeout(resolve, 100));

  finished = true;
  return 'OK';
}
const fail = async function() {
  await new Promise(resolve => setTimeout(resolve, 10));
  throw new Error('Oops');
}
try {
  await Promise.all([success(), fail()]);
} catch (err) {
  err.message; // 'Oops'

  // `Promise.all()`马上失败，因为`fail()`抛出错误
  // 但`success()`仍在运行
  finished; // false

  // 等一会，`success()`会设置`finished`
  await new Promise(resolve => setTimeout(resolve, 100));
  finished; // true
}
```

## 来点花哨的[Generator](https://thecodebarbarian.com/introducing-80-20-guide-to-es2015-generators)

`Promise.all()`函数不止可以用数组，参数可以是任何[可迭代对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all#Syntax)。数组是可迭代的，Generator函数也是可迭代的。也就是说可以传递一个yields Promise的Generator，`Promise.all()`会把所有yields出来的Promise包装成单个的Promise。

```javascript
const generatorFn = function*() {
  for (let i = 1; i <= 5; ++i) {
    yield Promise.resolve(i);
  }
}

const arr = await Promise.all(generatorFn());

arr; // [1, 2, 3, 4, 5]
```

*async/await是JavaScript中并发的未来趋势。[“Mastering Async/Await”](http://asyncawait.net/)教你如何在几个小时内，用async/await构建前后端APP，来看看吧！*
