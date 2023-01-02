---
title: Javascript获取当前时间戳
date: 2023-01-02 15:55:50
categories:
- 前端
tags:
- 时间戳
- Javascript
---

原文：[Get the Current Timestamp in JavaScript](https://masteringjs.io/tutorials/fundamentals/timestamps)

<!-- more -->

Unix格式时间戳，是从1970年1月1日开始计算的秒数。Unix时间戳是一种通用的表示时间点的方法，因为它只需要64bit（2038年前为32bit），可以使用数学比较符（例如>和<=）进行比较，而且是与时区无关的。

Javascript的`Date.now()`函数返回自1970年1月1日开始的毫秒数，换言之，`Date.now()`并不是Unix时间戳，但你可以很容易的通过除以1000进行转换：

```javascript
// 1556372741848, _毫秒_ 从1970年1月1日开始
Date.now();

// 1556372741, _秒_ 从1970年1月1日开始。 得出Unix时间戳
Math.floor(Date.now() / 1000);
```

已知一个日期，你可以使用`getTime()`函数或`valueOf()`函数取得1970年1月1日开始的毫秒数。这两个函数是等效的。

```javascript
const d = new Date('2019-06-01');

// 两个都能得出从Unix纪元开始的毫秒数
d.getTime(); // 1559347200000
d.valueOf(); // 1559347200000
```

`getTime()`和`valueOf()`这两个函数实现相同的功能，原因见：[Javascript使用valueOf()函数进行隐式类型转换](/post/javascript-uses-valueof-function-for-implicit-type-conversions/)
