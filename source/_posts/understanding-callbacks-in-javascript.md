---
title: 理解JavaScript的回调机制
date: 2023-01-16 13:55:18
categories:
- Mastering JS
hide: true
tags:
- callback
---

原文：[Understanding Callbacks in JavaScript](https://masteringjs.io/tutorials/fundamentals/callbacks)

“回调”是JavaScript中较其他语言不同的几个概念之一。本文围绕回调帮助你缕清思路。

<!-- more -->

JavaScript常见的回调模式，是把内联函数作为参数传递给另一个函数。例如：

```javascript
[1, 2, 3].forEach(function callback(v) {
  console.log(v); // 1, 2, 3
});

fetch('https://google.com').then(function callback(res) {
  console.log(res);
});

new EventEmitter().on('event', function callback(ev) {
  console.log(ev);
});
```

最直观的讲，JavaScript的回调不是你主动调用，而是由其他代码调用的函数。“其他代码”可能是JavaScript的内置函数，比如`setTimeout()`；可能是一个框架，比如`Express`；可能是一个库，比如`axios`；可能是任何JavaScript中的代码。回调是*响应式*的，而非*命令式*的：你定义一个函数，它会在另一块代码中调用，但你不对调用这个函数负责。

```javascript
// JavaScript运行时负责在5秒后调用 `callback`
setTimeout(function callback() {
  console.log('Hello, World!');
}, 5000); 

const app = require('express')();

// Express负责以正确的参数调用 `callback`
app.get('/', function callback(req, res) {
  res.send('Hello, World!');
});
```

## Node风格回调

[Promise](https://masteringjs.io/tutorials/fundamentals/promise)相对较晚加入JavaScript，首次加入在2015年。在Promise和async/await之前，Node.js中事实标准的异步操作范例，是传递一个回调函数，带2个参数，一个错误err和一个结果res。当说到[“回调地狱”](https://thecodebarbarian.com/2015/03/20/callback-hell-is-a-myth)时，其实说的就是Node风格的回调。

例如，亚马逊AWS S3的Node.js SDK只支持回调。为了实现上传文件完成时发起提醒，需要传递一个回调函数。

```javascript
const AWS = require('aws-sdk');
const fs = require('fs');

AWS.config.update({
  accessKeyId: process.env.AWS_ACCESS_KEY_ID,
  secretAccessKey: process.env.AWS_SECRET_ACCESS_KEY
});

const s3 = new AWS.S3();

const options = {
  Bucket: process.env.AWS_BUCKET,
  Body: fs.createReadStream('./package.json'),
  Key: 'package.json'
};

// 传递一个 `callback`，带2个参数：`err` 和 `res`。
// 如果`err`为 null 或 undefined，可以认为是上传成功。
s3.upload(options, function callback(err, res) {
  if (err != null) {
    // 如果 `err` 不为空，应该处理错误
  }
});
```

Node风格回调的主要作用，是告诉你一个异步操作成功或失败，如果成功，结果是什么。Promise在这个方面已经取代了Node风格回调，因为嵌套的回调在语法上非常令人难受，而且Node风格的回调从未形成标准化。

## 回调转换为Promise

[Node.js内置了promisify()函数](https://nodejs.org/api/util.html#util_util_promisify_original)，可以把基于回调的API转换成Promise风格。使用promisify()将一个基于Node回调的函数，转换为一个返回Promise的函数，如下例：

```javascript
function myCallbackBasedFunction(callback) {
  setTimeout(() => callback(null, 'Hello, World!'), 25);
}

const util = require('util');
const myPromiseBasedFunction =
  util.promisify(myCallbackBasedFunction);

await myPromiseBasedFunction(); // 'Hello, World!'
```