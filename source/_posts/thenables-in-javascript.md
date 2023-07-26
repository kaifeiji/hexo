---
title: JavaScript的Thenable
date: 2023-07-25 15:27:56
categories:
- Mastering JS
tags:
- Promise
---

原文：[Thenables in JavaScript](https://masteringjs.io/tutorials/fundamentals/thenable)

Thenable是一种行为类似于Promise的对象，主要用于链式操作和async/await，但它并不是Promise。

<!-- more -->

JavaScript中，Thenable是一种包含[`then()`方法](https://masteringjs.io/tutorials/fundamentals/then)的对象。所有Promise都是Thenable，但并非所有Thenable都是Promise。

许多Promise的模式，比如[链式操作](https://masteringjs.io/tutorials/fundamentals/promise-chaining)和[async/await](https://masteringjs.io/tutorials/fundamentals/async-await)，可以用Thenable实现。例如，用Thenable实现一个Promise链：

```javascript
// Thenable是包含`then()`方法的对象。
// 以下Thenable可以像Promise一样，
// 在10ms后fulfilled，返回42
const thenable = {
  then: function(onFulfilled) {
    setTimeout(() => onFulfilled(42), 10);
  }
};

Promise.resolve().
  then(() => thenable).
  then(v => {
    v; // 42
  });
```

也可以在`await`中使用Thenable：

```
// Thenable是包含`then()`方法的对象。
// 以下Thenable可以像Promise一样，
// 在10ms后fulfilled，返回42
const thenable = {
  then: function(onFulfilled) {
    setTimeout(() => onFulfilled(42), 10);
  }
};

const v = await thenable;
v; // 42
```

## Thenable的应用

许多库实现了Thenable来支持async/await。例如，[Mongoose的查询](https://masteringjs.io/tutorials/mongoose/query)就是Thenable，而`exec()`函数返回的是Promise。[Superagent](https://visionmedia.github.io/superagent/)是一个流行的HTTP客户端，也是用了Thenable。Mongoose的查询和Superagent的请求都不是Promise。

其他一些库直接使用Promise。例如，[axios的请求就是Promise](https://masteringjs.io/tutorials/axios/then)，`instanceof Promise`会返回true。

可以把任意Thenable用Promise.resolve()转换成一个Promise：

```javascript
// Thenable是包含`then()`方法的对象。
// 以下Thenable可以像Promise一样，
// 在10ms后fulfilled，返回42
const thenable = {
  then: function(onFulfilled) {
    setTimeout(() => onFulfilled(42), 10);
  }
};

const p = Promise.resolve(thenable);
p instanceof Promise; // true

const v = await p;
v; // 42
```

*async/await是JavaScript中并发的未来趋势。[“Mastering Async/Await”](http://asyncawait.net/)教你如何在几个小时内，用async/await构建前后端APP，来看看吧！*