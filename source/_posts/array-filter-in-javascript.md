---
title: JavaScript数组的filter()
date: 2023-02-07 19:47:35
categories:
- Mastering JS
tags:
- filter
---

原文：[Array `filter()` in JavaScript](https://masteringjs.io/tutorials/fundamentals/filter)

JavaScript的数组函数`filter()`可以筛选出符合指定条件的数组元素，本文简述该函数的使用。

<!-- more -->

[`Array#filter()`函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)接收一个[`callback`回调函数](https://masteringjs.io/tutorials/fundamentals/callbacks)，返回一个新的数组，其中的元素在`callback`函数中返回值为真值。

```javascript
const numbers = [1, 2, 3, 4, 5, 6];

let callback = v => v % 2 === 0;
const even = numbers.filter(callback);
even; // [2, 4, 6]

callback = v => v % 2 === 1;
const odd = numbers.filter(callback);
odd; // [1, 3, 5]
```

注意：`filter()`返回的是新数组，它**不会**修改原数组。

```javascript
even === numbers; // false
odd === numbers; // false
even === odd; // false

numbers.length; // 6, `filter()` does not modify `numbers`
```

`filter()`有点像[浅拷贝](https://masteringjs.io/tutorials/fundamentals/shallow-copy)：它只是克隆了顶层的数组，而嵌套的对象不会改变。

```javascript
const people = [
  { name: 'Jean-Luc Picard', rank: 'Captain' },
  { name: 'Will Riker', rank: 'Commander' },
  { name: 'Geordi La Forge', rank: 'Lieutenant' }
];

const filtered = people.filter(p => p.rank === 'Lieutenant');

// 虽然`filtered`是一个新数组，但其中的元素仍然指向与原数组相同的对象
// 因此修改`filtered`中的对象，会影响原数组
filtered[0] === people[2]; // true
filtered[0].rank = 'Lieutenant Commander';
people[2].rank; // 'Lieutenant Commander'
```