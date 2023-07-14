---
title: JavaScript中Promise的resolve
date: 2023-02-18 20:40:01
categories:
- Mastering JS
hide: true
tags:
- Promise
- resolve
---

原文：[Resolve a Promise in JavaScript](https://masteringjs.io/tutorials/fundamentals/promise-resolve)

在JavaScript的Promise中，resolve一个值时，会将Promise状态标记为fulfilled。

<!-- more -->

[`Promise.resolve()`函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/resolve)可以非常简洁的创建一个[fulfilled状态的Promise](https://masteringjs.io/tutorials/fundamentals/promise#promises-as-state-machines)，返回给定的参数值。例如，要创建一个fulfilled状态的Promise，返回字符串`'Hello, World'`：

```javascript
const p = Promise.resolve('Hello, World');

const str = await p;
str; // 'Hello, World'

return p.then(str => {
  str; // 'Hello, World'
});
```

## resolve和fulfilled不同

解释Promise的*resolve*和*fulfilled*的差异，是一个常见的JavaScript面试题。其中的差异很微妙，但也很重要。

主要的不同在于Promise调用resolve为另一个Promise。当调用`Promise.resolve(p)`时，如果`p`是一个Promise，就创建了一个新的绑定到`p`的Promise。如果`p`变为fulfilled，新的Promise也变为fulfilled，且有相同的返回值。如果`p`变为rejected，新的Promise也变为rejected，且有相同的返回值。[Promises/A+规范称之为“同化”](https://promisesaplus.com/#the-promise-resolution-procedure)。

```javascript
const p = Promise.resolve('Hello, World');
const p2 = Promise.resolve(p);

const str = await p2;
// `p2`同化为`p`的值.
str; // 'Hello, World'
```

一个Promise resolve为另一个Promise，它仍然是pending状态。在某些情况下，resolve后的Promise也能变为rejected！

```javascript
async function fail() {
  await new Promise(resolve => setTimeout(resolve, 100));
  throw new Error('Oops');
}

// 调用fail()返回一个Promise，在100毫秒后变为rejected。
// p也会变为rejected，即使它已经resolve过！
const p = Promise.resolve(fail());

const err = await p.catch(err => err);
err.message; // 'Oops'
```

resolve并不代表[Promise的状态](https://masteringjs.io/tutorials/fundamentals/promise#promises-as-state-machines)，而fulfilled才是Promise的3种状态之一，当Promise变为fulfilled，就会执行[`then()`函数](https://masteringjs.io/tutorials/fundamentals/then)传入的`onFulfilled`回调函数。

## Promise的constructor

用`new`创建一个Promise时，调用的是[Promise的constructor](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)。Promise的constructor接收一个参数——`executor`函数，Promise的constructor会执行这个`executor`函数，并传入2个参数：`resolve()`和`reject()`。

```javascript
function executor(resolve, reject) {
  typeof resolve; // 'function'
  typeof reject; // 'function'
}

new Promise(executor);
```

需要注意的是，第一个参数是`resolve()`，而**不是**`fulfill`。因为`resolve()`函数在Promise的constructor种表现和`Promise.resolve()`很相似，在Promise中用resolve()返回一个Promise，也就“同化”了这个Promise。

```javascript
const p = Promise.resolve('Hello, World');
const p2 = new Promise(resolve => resolve(p));

const str = await p2;
// `p2`同化了`p`的值.
str; // 'Hello, World'
```

*async/await是JavaScript中并发的未来趋势。[“Mastering Async/Await”](http://asyncawait.net/)教你如何在几个小时内，用async/await构建前后端APP，来看看吧！*
