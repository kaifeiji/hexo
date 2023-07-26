---
title: 用forEach()遍历对象
date: 2023-07-26 16:56:42
categories:
- Mastering JS
hide: true
tags:
- Object
- iterate
---

原文：[Iterating Through an Object with `forEach()`](https://masteringjs.io/tutorials/fundamentals/foreach-object)

通过Object.keys()、Object.values()和Object.entries()，可以用forEach遍历一个JavaScript对象。

<!-- more -->

[JavaScript中的`Array#forEach()`函数](https://masteringjs.io/tutorials/fundamentals/foreach)可以遍历[数组](http://thecodebarbarian.com/the-80-20-guide-to-javascript-arrays.html)，无法遍历对象。不过可以用`Object.keys()`、`Object.values()`和`Object.entries()`将对象转换为数组，然后[用forEach()进行遍历](https://masteringjs.io/tutorials/fundamentals/iterate-object)。

## Object.keys()

`Object.keys()`函数返回对象自身可枚举属性名的数组，用`forEach()`可以遍历每一个属性名。

```javascript
const obj = {
  name: 'Jean-Luc Picard',
  rank: 'Captain'
};

// 打印 "name Jean-Luc Picard" 和 "rank Captain"
Object.keys(obj).forEach(key => {
  console.log(key, obj[key]);
});
```

## Object.values()

`Object.values()`函数返回对象自身可枚举属性值的数组。用`forEach()`可以遍历它返回的对象属性值的数组。

```javascript
const obj = {
  name: 'Jean-Luc Picard',
  rank: 'Captain'
};

// 打印 "Jean-Luc Picard" 和 "Captain"
Object.values(obj).forEach(val => {
  console.log(val);
});
```

## Object.entries()

`Object.entries()`函数返回对象属性`entry`的数组。`entry`是长度为2的数组，`entry[0]`是属性名，`entry[1]`是属性值。用`forEach()`可以同时遍历属性名和属性值：

```javascript
const obj = {
  name: 'Jean-Luc Picard',
  rank: 'Captain'
};

// 打印 "name Jean-Luc Picard" 和 "rank Captain"
Object.entries(obj).forEach(entry => {
  const [key, value] = entry;
  console.log(key, value);
});
```

*本教程对您有帮助吗？来[GitHub仓库](https://github.com/mastering-js/masteringjs.io)点个星支持我们吧！*