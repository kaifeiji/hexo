---
title: JavaScript的真值
date: 2023-02-09 19:42:53
categories:
- Mastering JS
hide: true
tags:
- true
---

原文：[Truthy Values in JavaScript](https://masteringjs.io/tutorials/fundamentals/truthy)

JavaScript的if条件不一定是Boolean值，可以是任何类型的值。内置的强制类型转换接收到一个真值，会转换为true。

<!-- more -->

JavaScript内置的类型强制转换，会将真值转换为`true`。每一个值，要么是真值，要么是[假值](https://masteringjs.io/tutorials/fundamentals/falsy)，任何不是假值的必然是真值。

真假经常出现在if语句中。例如，以下if语句只有在`v`是真值时才能打印。

```javascript
if (v) {
  console.log('v是真值！');
}
```

记住，[JavaScript只有7个假值](https://masteringjs.io/tutorials/fundamentals/falsy)：

* `false`
* `0`
* `0n`: [BigInt](http://thecodebarbarian.com/an-overview-of-bigint-in-node-js.html) 0
* `''`: 空字符串
* `null`
* `undefined`
* `NaN`

任何其他值都是真值。例如，即便是[用`false`构建的`Boolean`对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Boolean)，也是真值。

```javascript
const v = new Boolean(false);

// 会打印！所有JavaScript对象都是真值。
if (v) {
  console.log('v是真值！');
}
```

## 逻辑非运算符

JavaScript的[逻辑非运算符](https://masteringjs.io/tutorials/fundamentals/logical-operators#logical-not)将真值转换为`false`，将假值转换为`true`。换言之，逻辑非运算符`!`可以获得JavaScript内置强制类型转换的结果。

```javascript
// 如果`v`是真值，`b`是false
const b = !v;
```

你可能也见过两个逻辑非运算符：`!!v`。这样可以将一个任意值转换为`Boolean`类型。

```javascript
// 只有`v`是真值时，`isTruthy`才是true。
const isTruthy = !!v;
```
