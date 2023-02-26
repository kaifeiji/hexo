---
title: JavaScript计算标准差
date: 2023-01-04 19:43:49
categories:
- Mastering JS
tags:
- JavaScript
- 标准差
- Standard Deviation
---

原文：[Calculate Standard Deviation in JavaScript](https://masteringjs.io/tutorials/fundamentals/stddev)

在Node.js和浏览器中使用Math.js计算标准差。

<!-- more -->

[标准差](https://en.wikipedia.org/wiki/Standard_deviation)是度量一组数值与平均值相差多少的度量值。标准差小，说明所有数值相对接近平均值。JavaScript没有内置的标准差函数，[Math.js](https://www.npmjs.com/package/mathjs)作为一个支持良好的库，包含全特性的标准差函数。

以下是使用[Math.js的std()函数](https://mathjs.org/docs/reference/functions/std.html)计算标准差的例子。

```javascript
const math = require('mathjs');

// 将数组作为参数传入 `std()` 函数：
math.std([5, 5, 5, 5]); // 0

// 或者传入一组参数（也可以称为参数展开）
math.std(1, 5, 9); // 4
```

Math.js还支持偏移校正。Math.js的`std()`函数默认使用[贝塞尔校正](https://en.wikipedia.org/wiki/Bessel%27s_correction)，也能接收第二个参数`normalization`来配置校正方法。按照默认的贝塞尔校正，`std()`函数以`n-1`作为除数计算标准差。第二个参数为`'uncorrected'`时，`std()`的除数是`n`，第二个参数为`'biased'`时，`std()`的除数是`n+1`。

```javascript
const math = require('mathjs');

// 传入校正参数时，必须使用数组传值
math.std([1, 3], 'uncorrected'); // 1
math.std([2, 4, 6, 8], 'biased'); // 2
```
