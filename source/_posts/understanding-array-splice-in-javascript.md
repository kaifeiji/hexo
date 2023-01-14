---
title: 理解JavaScript中的Array.splice()
date: 2023-01-14 20:28:44
categories:
- Mastering JS
tags:
- splice
---

原文：[Understanding Array.splice() in JavaScript](https://masteringjs.io/tutorials/fundamentals/array-splice)

如何使用`splice()`从JavaScript数组中添加和移除元素。

<!-- more -->

[Array#splice()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/splice)函数可以修改原始数组，添加或移除元素。最常用的是从数组中移除元素，但也能用于向数组中间添加元素。

## 移除元素

`splice()`函数是唯一一个，不创建新数组，从原数组中间移除元素的原生数组函数。例如，数组`['a', 'b', 'c', 'd']`，用`splice()`移除`'c'`。

```javascript
const arr = ['a', 'b', 'c', 'd'];

// 从index 2开始移除1个元素
arr.splice(2, 1);

arr; // ['a', 'b', 'd']
```

`splice()`的前2个参数是`start`和`deleteCount`。`start`参数代表`splice()`从数组的哪个位置开始操作。`deleteCount`参数代表`splice()`移除多少个元素。

除了`splice()`，JavaScript项目中也可以见到用`filter()`从数组移除元素。两种方法的主要区别是`filter()`会创建一个新数组。这意味着在依赖不可变性的应用中（例如[React](https://www.npmjs.com/package/react)应用），`filter()`是更好的选择。

```javascript
const arr = ['a', 'b', 'c', 'd'];

// 从index 2开始，移除1个元素
const arr2 = arr.filter((v, i) => i !== 2);

// `arr` 仍然包含 'c', 因为 `filter()` 没有修改原数组。
// 而 `splice()` 会修改原数组。
arr; // ['a', 'b', 'c', 'd']
arr2; // ['a', 'b', 'd']
arr2 === arr; // false
```

## 在数组中间添加元素

`splice()`函数也可以在数组中间添加元素。JavaScript数组的[push()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/push)函数可以在数组末尾添加元素，[unshift()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/unshift)函数可以在开头添加元素。`splice()`函数是唯一一种原生的，可以在数组中间添加元素的方法。

例如，数组`['a', 'b', 'd']`，要在`'b'`后面添加`'c'`。`splice()`的`deleteCount`参数之后的每个参数，都会从`startIndex`开始添加到数组中。因此要插入`'c'`，`splice()`的`deleteCount`是0，第三个参数是`'c'`。

```javascript
const arr = ['a', 'b', 'd'];

let start = 2;
let deleteCount = 0;
arr.splice(start, deleteCount, 'c');

arr; // ['a', 'b', 'c', 'd'];
```