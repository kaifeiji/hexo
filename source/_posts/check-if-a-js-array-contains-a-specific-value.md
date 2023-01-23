---
title: 检查JS数组是否包含特定值
date: 2023-01-20 10:06:57
categories:
- Mastering JS
tags:
- Array
---

原文：[Check if a JS Array Contains a Specific Value](https://masteringjs.io/tutorials/fundamentals/includes)

检查JavaScript数组是否包含某个值，有两种常见方法：`includes()`和`indexOf()`。本文展示了如何使用二者，以及二者分别适用的场景。

<!-- more -->

一个[JavaScript数组](http://thecodebarbarian.com/the-80-20-guide-to-javascript-arrays.html)，有两种内置的数组方法可以确定数组是否包含指定元素。例如有3个元素的简单数组：

```javascript
const arr = ['A', 'B', 'C'];
```

要确定`arr`是否包含字符串`'B'`，可以使用[`Array#includes()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/includes)或[`Array#indexOf()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/indexOf)。

## includes()

`Array#includes()`[从ESMAScript 2016开始引入](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/includes#Specifications)。它接收一个参数`valueToFind`，如果数组中存在等于`valueToFind`的元素则返回`true`。

```javascript
const arr = ['A', 'B', 'C'];

arr.includes('B'); // true
arr.includes('D'); // false
```

`Array#includes()`函数检查是否相等，与`===`运算符（不进行类型强制转换）有基本相同的语义。有一个例外是`NaN`，`Array#includes()`函数会在数组中找到`NaN`（译注：NaN !== NaN）。这种相等检查的学名叫做[`sameValueZero`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Equality_comparisons_and_sameness#Same-value-zero_equality)。

```javascript
// 数组包含1个元素, 'NaN'
const arr = [parseInt('foo')];

arr.includes(parseInt('foo')); // true
arr.includes(NaN); // true
arr.includes(Number.NaN); // true

// `===` 运算符对于NaN有一些怪异行为.
// `Array#includes()` 调和了这个小问题.
arr[0] === NaN; // false
arr[0] === Number.NaN; // false
```

## indexOf()

`Array#indexOf()`函数也很常见，可以替代`includes()`。`indexOf()`函数返回数组找到的第一个`valueToFind`的index，找不到返回`-1`。

```javascript
const arr = ['A', 'B', 'C'];

arr.indexOf('A'); // 0
arr.indexOf('B'); // 1
arr.indexOf('D'); // -1

// 检查数组是否包含给定值，使用以下代码
arr.indexOf('D') !== -1; // false
```

要检查`arr`是否包含`v`，使用`arr.indexOf(v) !== -1`。在一些代码中，可能会看到`~arr.indexOf(v)`，`~`代表[位操作符非](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators#(Bitwise_NOT))。

一个整数v，`~v === -(v + 1)`，如果`v === -1`那么`~v === 0`。这是一种方便的小技巧，可以不用写`!== -1`。但是使用位操作一般而言是不好的习惯，只是少输入了4个字符，但牺牲了可读性。

```javascript
const arr = ['A', 'B', 'C'];

if (~arr.indexOf('A')) {
  // 进入if
}
if (~arr.indexOf('D')) {
  // 不进入if
}
```

与`Array#includes()`不同，`Array#indexOf()`检查相等时，与`===`运算符有完全相同的语义。也就是说，`Array#indexOf()`无法在数组中查找NaN。

```javascript
// 数组包含1个元素, NaN
const arr = [parseInt('foo')];

arr.indexOf(NaN); // -1
arr.indexOf(Number.NaN); // -1
```

`Array#includes()`通常是更好的选择，因为不需要输入`!== -1`，而且在判断相等时有略微的优势。但`Array#includes()`时从ES2016才引入，[所有的IE](https://caniuse.com/#feat=array-includes)、6.0.0之前版本的Node.js是不支持的。如果使用`Array#includes()`，要确保[为老版本浏览器添加polyfill](https://www.npmjs.com/package/array-includes)。
