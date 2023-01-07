---
title: JavaScript获取当前时间戳
date: 2023-01-02 15:55:50
categories:
- Mastering JS
tags:
- 时间戳
- JavaScript
---

原文：[Get the Current Timestamp in JavaScript](https://masteringjs.io/tutorials/fundamentals/timestamps)

学些如何在JavaScript中获取当前Unix时间戳。

<!-- more -->

[Unix格式时间戳](https://en.wikipedia.org/wiki/Unix_time)，是从1970年1月1日开始计算的秒数。Unix时间戳是一种通用的表示时间点的方法，因为它只需要64bit（[2038年前为32bit](https://developers.slashdot.org/story/13/01/22/1447205/youve-got-25-years-until-unix-time-overflows)），可以使用数学比较符（例如`>`和`<=`）进行比较，而且是与时区无关的。

JavaScript的[Date.now()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/now)函数返回自1970年1月1日开始的毫秒数，换言之，`Date.now()`并不是Unix时间戳，但你可以很容易的通过除以1000进行转换：

```javascript
// 1556372741848, _毫秒_ 从1970年1月1日开始
Date.now();

// 1556372741, _秒_ 从1970年1月1日开始。 得出Unix时间戳
Math.floor(Date.now() / 1000);
```

已知一个日期，你可以使用[getTime()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/getTime)函数或[valueOf()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/valueOf)函数取得1970年1月1日开始的毫秒数。这两个函数是等效的。

```javascript
const d = new Date('2019-06-01');

// 两个都能得出从Unix纪元开始的毫秒数
d.getTime(); // 1559347200000
d.valueOf(); // 1559347200000
```

`getTime()`和`valueOf()`这两个函数实现相同的功能，原因见：[JavaScript使用valueOf()函数进行隐式类型转换](https://blog.klipse.tech/javascript/2016/09/21/valueOf-js.html)
