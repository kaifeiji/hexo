---
title: JavaScript中的“假”值是什么
date: 2023-02-08 22:19:17
categories:
- Mastering JS
hide: true
tags:
- false
---

原文：[What is "Falsy" in JavaScript?](https://masteringjs.io/tutorials/fundamentals/falsy)

JavaScript的类型强制转换导致代码很容易出错，但如果理解了基本原理，可以省去很多无用的类型检查。本文简述了假值。

<!-- more -->

在JavaScript中，如果一个值，内置类型强制转换为`false`，那么称之为[“假”值](https://developer.mozilla.org/en-US/docs/Glossary/Falsy)。例如，分析以下的if语句：

```javascript
if (v) {
  console.log('v is not falsy');
}
```

`v`**不是**以下值时，`console.log()`才能运行：

* `false`
* `0`
* `0n`: [BigInt](http://thecodebarbarian.com/an-overview-of-bigint-in-node-js.html) 0
* `''`: 空字符串
* `null`
* `undefined`
* `NaN`

这7个值就是JavaScript中的假值，其他任何值都是[真值](https://developer.mozilla.org/en-US/docs/Glossary/truthy)。

其中，非`null`的[对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Working_with_Objects)总是真值，即便它的[`valueOf()`函数](https://masteringjs.io/tutorials/fundamentals/valueof)返回一个假值。

```javascript
function isFalsy(v) {
  return !v;
}

// 不是假值。用`0`构建的Number对象也是真值，虽然0是假值。
isFalsy(new Number(0));
```

## 建议

在if语句中用隐性的类型转换得到真/假值，是典型的混乱之源。大部分场景下，7个假值与你所需的判断是不匹配的。

例如，要实现一个检查字符串是否少于25个字符的函数。

```javascript
function checkLength(v) {
  if (!v) {
    throw new Error('必须提供一个字符串！');
  }
  return v.length < 25;
}
```

出乎意料的是，`checkLength('')`会抛出错误，因为空字符串是假值。应该像这样检查`v`是不是字符串：

```javascript
function checkLength(v) {
  if (typeof v !== 'string') {
    throw new Error('必须提供一个字符串！');
  }
  return v.length < 25;
}
```

## 空值

除了检查真/假值，通常更多的是检查“空”值。一个常见的检查空值的例子是，在获取`v`的属性时，确保不会报错：`TypeError: Cannot read property 'xxx' of null`

除了`null`和`undefined`，`v`是其他值时访问`v.prop`都不会报错，即使`NaN.prop`也不会报错。

```javascript
const x = Number('abc');
x; // NaN
x.prop; // undefined
```

检查`v == null`等价于`v === null || v === undefined`。也就是说，只有在值是`null`或`undefined`时，它才`== null`。检查`v == null`，在空值检查中，比真假值更精确。
