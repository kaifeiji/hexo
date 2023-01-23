---
title: JavaScript数组的push教程
date: 2023-01-21 10:27:48
categories:
- Mastering JS
tags:
- push
---

原文：[JavaScript Array Push Tutorial](https://masteringjs.io/tutorials/fundamentals/push)

push()方法向JavaScript数组的末尾添加一个元素。本文介绍push()是如何使用的。

<!-- more -->

JavaScript中，[Array#push()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/push)方法将接收的参数添加到数组的末尾，返回添加元素后数组的长度。

```javascript
const arr = ['A', 'B', 'C'];

arr.push('D'); // 4
arr; // ['A', 'B', 'C', 'D']

arr.push('E', 'F'); // 6
arr; // ['A', 'B', 'C', 'D', 'E', 'F']
```

## 使用展开运算符

假设要在数组`arr`的末尾，添加另一个数组`arr2`的所有元素。`arr.push(arr2)`并不会添加`arr2`的元素，而是将数组`arr2`作为一个元素添加进去。

```javascript
const arr = ['A', 'B', 'C'];
const arr2 = ['D', 'E'];

arr.push(arr2); // 4
arr; // ['A', 'B', 'C', ['D', 'E']]
```

要把`arr2`的元素添加到`arr`的末尾，使用[展开运算符](https://thecodebarbarian.com/object-assign-vs-object-spread.html)。可以认为`...`把数组转换成了位置参数。

```javascript
const arr = ['A', 'B', 'C'];
const arr2 = ['D', 'E'];

// 等效于 `arr.push('D', 'E')`
arr.push(...arr2); // 5
arr; // ['A', 'B', 'C', 'D', 'E']
```

> 译注：用forEach或map更直观一些。

```javascript
const arr = ['A', 'B', 'C'];
const arr2 = ['D', 'E'];

arr2.forEach((item)=>{
    arr.push(item);
});
```