---
title: 使用bluebird操作Promise
date: 2023-03-03 09:46:49
categories:
- Mastering JS
tags:
- bluebird
---

原文：[Using Bluebird Promises](https://masteringjs.io/tutorials/fundamentals/bluebird)

blurbird是JavaScript一个流行的Promise库。

<!-- more -->

[bluebird](http://bluebirdjs.com/docs/getting-started.html)是一个流行的JavaScript的Promise库，它是[原生Promise](https://masteringjs.io/tutorials/fundamentals/promise)的替代品。

```javascript
global.Promise = require('bluebird');

// 打印 "42"
Promise.resolve(42).then(val => console.log(val));
```

人们为什么使用bluebird而非原生Promise？有2个原因：

1、性能：

早期的原生Promise实现很慢——以下基准测试代码显示，在Node.js 8中，创建一个原生Promise比创建一个bluebird的Promise慢3倍：

```javascript
// global.Promise = require('bluebird');
const Benchmark = require('benchmark');

const suite = new Benchmark.Suite();

// 添加测试
suite.
  add('new promise', function() {
    return new Promise((resolve, reject) => {});
  }).
  on('cycle', function(event) {
    console.log(String(event.target));
  }).
  on('complete', function() {
    console.log('Fastest is ' + this.filter('fastest').map('name'));
  }).
  run();
```

以下是输出内容，首先是blurbird，然后是原生Promise：

```bash
$ ~/Workspace/libs/node-v8.17.0-linux-x64/bin/node ./bluebird.js 
new promise x 36,846,162 ops/sec ±0.66% (95 runs sampled)
Fastest is new promise
$
$ ~/Workspace/libs/node-v8.17.0-linux-x64/bin/node ./bluebird.js 
new promise x 12,244,609 ops/sec ±1.80% (84 runs sampled)
Fastest is new promise
```

然而在Node.js 12.x中，原生Promise已经明显比bluebird要快了。

2、更完整的堆栈追踪

bluebird内置了对[异步堆栈追踪](https://thecodebarbarian.com/async-stack-traces-in-node-js-12)的支持。例如，以下代码不会打印`fn()`是在哪一行调用的：

```javascript
Promise.resolve().
  then(fn).
  catch(err => console.log(err));

function fn() {
  return new Promise((resolve, reject) => {
    setImmediate(() => reject(new Error('Oops')));
  });
}
```

输出如下：

```bash
$ node ./test
Error: Oops
    at Immediate.setImmediate [as _onImmediate] (/app/test.js:8:31)
    at runCallback (timers.js:705:18)
    at tryOnImmediate (timers.js:676:5)
    at processImmediate (timers.js:658:5)
```

但是用bluebird，可以像以下代码，开启完整堆栈追踪。

```javascript
global.Promise = require('bluebird');
global.Promise.config({ longStackTraces: true });

Promise.resolve().
  then(fn).
  catch(err => console.log(err));

function fn() {
  return new Promise((resolve, reject) => {
    setImmediate(() => reject(new Error('Oops')));
  });
}
```

运行以上代码会返回如下堆栈追踪，其中包含了`fn()`调用的行号：

```bash
$ node ./test
Error: Oops
    at Immediate.setImmediate [as _onImmediate] (/app/test.js:10:31)
From previous event:
    at fn (/app/test.js:9:10)
    at runCallback (timers.js:705:18)
    at tryOnImmediate (timers.js:676:5)
    at processImmediate (timers.js:658:5)
From previous event:
    at Object.<anonymous> (/app/test.js:5:3)
    at Module._compile (internal/modules/cjs/loader.js:778:30)
    at Object.Module._extensions..js (internal/modules/cjs/loader.js:789:10)
    at Module.load (internal/modules/cjs/loader.js:653:32)
    at tryModuleLoad (internal/modules/cjs/loader.js:593:12)
    at Function.Module._load (internal/modules/cjs/loader.js:585:3)
    at Function.Module.runMain (internal/modules/cjs/loader.js:831:12)
    at startup (internal/bootstrap/node.js:283:19)
    at bootstrapNodeJSCore (internal/bootstrap/node.js:623:3)
```

## 与async/await整合

比较不好的一点，是[async函数](https://thecodebarbarian.com/async-functions-in-javascript.html)无法返回bluebird的Promise。即使设置`global.Promise = require('bluebird');`，async函数也会返回原生的Promise。

```javascript
const NativePromise = global.Promise;
global.Promise = require('bluebird');

async function run() { return 'Hello, World'; }

const p = run();
p instanceof NativePromise; // true
p instanceof global.Promise; // false
```

## 应该用bluebird还是原生Promise？

2020年的现实情况是，大部分JavaScript的应用采用bluebird并没有太大优势。bluebird在Node.js和现代浏览器中，已经没有比原生Promise更好的性能优势——原生Promise实际上更快。然而，在老版本的浏览器和Node.js中，bluebird可以保证稳定的性能表现。

*async/await是JavaScript中并发的未来趋势。[“Mastering Async/Await”](http://asyncawait.net/)教你如何在几个小时内，用async/await构建前后端APP，来看看吧！*
