---
title: 让JavaScript函数暂停一段时间
date: 2023-02-07 00:12:47
categories:
- Mastering JS
tags:
- sleep
---

原文：[Make a JavaScript Function Sleep](https://masteringjs.io/tutorials/fundamentals/sleep)

JavaScript没有内置的`sleep()`函数，但也可以通过其他方式让函数暂停一段时间。

<!-- more -->

许多语言有`sleep()`函数，JavaScript却没有暂停执行一段时间的内置函数。主要原因是：在C一类的语言中，`sleep(2)`阻塞当前线程2秒钟，而JavaScript是单线程的，C风格的`sleep(2)`会阻塞整个JavaScript运行时2秒钟，是非常不实用的。

但是，在[异步函数](https://thecodebarbarian.com/async-functions-in-javascript.html)中，可以不阻塞JavaScript运行时，暂停一段时间。只需要`await`一个一段时间后`resolve`的Promise即可。

```javascript
function sleep(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}

async function run() {
  // 该异步函数暂停执行2秒钟
  await sleep(2000);

  console.log('已经暂停了2秒');
}
```

用[`new Promise()`构造器](https://masteringjs.io/tutorials/fundamentals/promise#the-promise-constructor)创建了一个新的`ms`毫秒后`resolve`的Promise。当`await`这个Promise时，就能暂停执行当前函数，不影响其他函数。

例如，可以在两个函数中并行调用`sleep()`，二者互相不会阻塞。

```javascript
const start = Date.now();

async function pauseMe() {
  await sleep(2000);
  console.log('毫秒数：', Date.now() - start);
}

// 打印 "毫秒数：2006"
pauseMe();
// 打印 "毫秒数：2010"
pauseMe();
```