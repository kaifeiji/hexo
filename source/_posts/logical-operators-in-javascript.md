---
title: JavaScript的逻辑运算符
date: 2023-02-09 14:52:03
categories:
tags:
---

原文：[Logical Operators in JavaScript](https://masteringjs.io/tutorials/fundamentals/logical-operators)

JavaScript有3种逻辑运算符：`&&`、`||`和`!`。这三个运算符常用在`if`语句，本文介绍了逻辑运算符其他的一些神奇的用法。

<!-- more -->

JavaScript有3种[逻辑运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_Operators)：

* `!`：逻辑非
* `&&`：逻辑与
* `||`：逻辑或

## 逻辑非

逻辑非运算符`!`可以检查一个值的[真](https://masteringjs.io/tutorials/fundamentals/truthy)/[假](https://masteringjs.io/tutorials/fundamentals/falsy)。简言之，真值的`!v === false`，以下7个为假值：

* `false`
* `0`
* `0n`: [BigInt](http://thecodebarbarian.com/an-overview-of-bigint-in-node-js.html) 0
* `''`: 空字符串
* `null`
* `undefined`
* `NaN`

```javascript
// 假值的 `!v === true`
!false; // true
!0; // true
!''; // true
!undefined; // true
!null; // true
!Number('abc'); // true

// 其他所有值都是真值，`!v === false`
!1; // false
!({}); // false
!({ valueOf: () => 0 }); // false
!' '; // false
```

与`||`和`&&`不同，非运算符总是返回一个Boolean。将一个值`v`转换为Boolean的常用方法，是进行两次逻辑非操作：

```javascript
// `v`转为Boolean
!!v;
```

## 逻辑或

逻辑或运算符`||`操作2个值，只要有一个是真值，就返回`true`。

```javascript
// `a`或`b`为true，则为true。
// `a`和`b`都为false，才是false。
!!(a || b);
```

逻辑或运算符常用于if语句，在多个条件中的一个为true时执行if语句块。但逻辑或运算符不一定返回一个Boolean：

* 如果`a`是真值，`(a || b) === a`
* 如果`a`是假值，`(a || b) === b`

多个或运算符看起来很简洁：`a || b || c || d`返回最靠左的真值。基于此，逻辑或常用于取默认值：

```javascript
const defaultNumReps = 2;

function repeat(str, numReps) {
  // 如果`numReps`是null、undefined或0
  // `numReps`会设置为默认值2
  numReps = numReps || defaultNumReps;

  let ret = '';
  for (let i = 0; i < numReps; ++i) {
    ret += str;
  }
  return ret;
}
```

## 逻辑与

逻辑与运算符`&&`操作2个值，只要有一个值是假值，就返回`false`。

```javascript
// `a`和`b`都为true，才是true
!!(a && b);
```

逻辑与运算符常用于if语句，在多个条件都满足时才执行if语句块。与逻辑或相似，逻辑与运算符也不一定返回一个Boolean：

* 如果`a`是假值，`(a && b) === a`
* 如果`a`是真值，`(a && b) === b`

多个逻辑与运算符的链`a && b && c && d`，会返回最靠左的假值。
