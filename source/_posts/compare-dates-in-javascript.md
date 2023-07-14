---
title: JavaScript比较日期
date: 2023-02-27 15:02:24
categories:
- Mastering JS
hide: true
tags:
- Date
---

原文：[Compare Dates in JavaScript](https://masteringjs.io/tutorials/fundamentals/compare-dates)

JavaScript比较日期不太容易，Date类型是对象，比较符按照引用而不是按值进行对比。本文简介如何用原生JavaScript比较日期。

<!-- more -->

有两个[`Date`对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)：

```javascript
const d1 = new Date('2019-06-01');
const d2 = new Date('2018-06-01');
const d3 = new Date('2019-06-01');
```

如何判断它们是否相等？奇怪的是，[三等和二等比较符](https://masteringjs.io/tutorials/fundamentals/equals)都无法比较两个日期。

```javascript
const d1 = new Date('2019-06-01');
const d2 = new Date('2018-06-01');
const d3 = new Date('2019-06-01');

d1 === d3; // false
d1 == d3; // false
```

要比较两个日期，要使用`toString()`或`valueOf()`。`toString()`方法把日期转换为ISO格式的日期字符串，`valueOf()`将日期转换为[毫秒时间戳](https://currentmillis.com/)。

```javascript
const d1 = new Date('2019-06-01');
const d2 = new Date('2018-06-01');
const d3 = new Date('2019-06-01');

// 'Fri May 31 2019 20:00:00 GMT-0400 (Eastern Daylight Time)'
d1.toString();
d1.valueOf(); // 1559347200000

d1.toString() === d2.toString(); // false
d1.toString() === d3.toString(); // true

d1.valueOf() === d2.valueOf(); // false
d1.valueOf() === d3.valueOf(); // true
```

## < 和 >

虽然`==`和`===`都不能判断两个日期是否相等，`<`和`>`却是可以比较日期的：

```javascript
d1 < d2; // false
d1 < d3; // false

d2 < d1; // true
```

要检查日期`a`是否早于日期`b`，可以检查`a < b`。

另一个技巧：你可以进行日期的减法。`a - b`返回的是两个日期相差的毫秒数。

```javascript
const d1 = new Date('2019-06-01');
const d2 = new Date('2018-06-01');
const d3 = new Date('2019-06-01');

d1 - d3; // 0
d1 - d2; // 1 year in milliseconds, 1000 * 60 * 60 * 24 * 365
```

换言之，可以用`a - b`比较两个日期，如果`b`晚于`a`，则`a - b < 0`。

## 排序

日期数组的排序会有一些奇怪，以下代码得出的竟然是倒序。

```javascript
const d1 = new Date('2017-06-01');
const d2 = new Date('2018-06-01');
const d3 = new Date('2019-06-01');

[d2, d1, d3].sort(); // [d3, d2, d1]
```

为什么呢？因为JavaScript的排序函数隐式的将数组中所有的值转换为字符串，再进行排序,以上代码中的`sort()`是基于以下字符串数组进行排序：

```javascript
[ 'Fri May 31 2019 20:00:00 GMT-0400 (Eastern Daylight Time)',
  'Thu May 31 2018 20:00:00 GMT-0400 (Eastern Daylight Time)',
  'Wed May 31 2017 20:00:00 GMT-0400 (Eastern Daylight Time)' ]
```

实际上，JavaScript会默认按日期的星期缩写（Mon、Tue、Wed、Thu、Fri、Sat、Sun）进行排序。

要按日期的早晚进行排序，需要向`sort()`函数传入一个`compare()`回调函数。`compare()`函数应该返回：

* `0` 如果`a`和`b`相等
* 正数 如果`a > b`
* 负数 如果`a < b`

前文提到日期可以做减法，可以用`a - b`直接作为比较函数：

```javascript
const d1 = new Date('2017-06-01');
const d2 = new Date('2018-06-01');
const d3 = new Date('2019-06-01');

[d2, d1, d3].sort((a, b) => a - b); // [d1, d2, d3]
```
