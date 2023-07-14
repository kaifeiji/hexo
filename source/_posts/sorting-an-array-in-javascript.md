---
title: JavaScript的数组排序
date: 2023-01-11 22:10:55
categories:
- Mastering JS
hide: true
tags:
- Array
- sort
---

原文：[Sorting an Array in JavaScript](https://masteringjs.io/tutorials/fundamentals/array-sort)

学习如何使用JavaScript内置的Array#sort()函数。

<!-- more -->

JavaScript的内置[sort()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)函数对初学者来说很奇怪。例如，对以下数组进行排序：

```javascript
[3, 20, 100].sort();
```

输出是什么呢？你可能认为数组会保持不变，但实际输出是这样的：

```javascript
[100, 20, 3];
```

因为JavaScript会先把数组元素转换为字符串，然后[根据JavaScript的字符串顺序进行排序](https://masteringjs.io/tutorials/fundamentals/string-compare)。

## 数值数组排序

`sort()`函数[接收一个参数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort#Parameters)，`compareFunction()`——对比函数。`compareFunction()`函数接收两个数组元素`a`和`b`。它返回：

* 如果`a < b`则返回负数
* 如果`a > b`则返回正数
* 如果`a`既不大于也不小于`b`则返回0

要按正序对一个数值数组排序，`compareFunction()`应该为`(a, b) => a - b`。

```javascript
const arr = [3, 20, 100];

arr.sort((a, b) => a - b);
arr; // [3, 20, 100]
```

要按倒序对一个数值数组排序，应该使用`(a, b) => b - a`。

```javascript
const arr = [20, 3, 100];

arr.sort((a, b) => b - a);
arr; // [100, 20, 3]
```

如果你对Java比较熟悉，可以认为`compareFunction()`和`compareTo()`起差不多的作用。

## 对象数组根据对象属性排序

假设你需要对一个对象数组进行排序。例如，有一份《星际旅行——下一代》的角色表：

```javascript
const characters = [
  { firstName: 'Jean-Luc', lastName: 'Picard', rank: 'Captain', age: 59 },
  { firstName: 'Will', lastName: 'Riker', rank: 'Commander', age: 29 },
  { firstName: 'Geordi', lastName: 'La Forge', rank: 'Lieutenant', age: 29 }
];
```

使用[JavaScript的字符串比较](https://masteringjs.io/tutorials/fundamentals/string-compare)，按照`lastName`对`characters`数组进行排序：

```javascript
const characters = [
  { firstName: 'Jean-Luc', lastName: 'Picard', rank: 'Captain', age: 59 },
  { firstName: 'Will', lastName: 'Riker', rank: 'Commander', age: 29 },
  { firstName: 'Geordi', lastName: 'La Forge', rank: 'Lieutenant', age: 29 }
];

characters.sort((a, b) => {
  if (a === b) {
    return 0;
  }
  return a.lastName < b.lastName ? -1 : 1;
});

// La Forge, Picard, Riker
characters;
```

按`age`对`characters`数组进行排序：

```javascript
const characters = [
  { firstName: 'Jean-Luc', lastName: 'Picard', rank: 'Captain', age: 59 },
  { firstName: 'Will', lastName: 'Riker', rank: 'Commander', age: 29 },
  { firstName: 'Geordi', lastName: 'La Forge', rank: 'Lieutenant', age: 29 }
];

characters.sort((a, b) => a.age - b.age);

// Riker, La Forge, Picard
characters;
```

如何按`rank`（军衔）排序呢？按军衔排序需要自定义顺序，因为JavaScript环境不知道`'Captain'`是比`'Lieutenant'`更高的军衔。使用`Map`自定义顺序可以实现军衔排序：

```javascript
const characters = [
  { firstName: 'Jean-Luc', lastName: 'Picard', rank: 'Captain', age: 59 },
  { firstName: 'Will', lastName: 'Riker', rank: 'Commander', age: 29 },
  { firstName: 'Geordi', lastName: 'La Forge', rank: 'Lieutenant', age: 29 }
];

const rankOrder = new Map([
  ['Captain', 1],
  ['Commander', 2],
  ['Lieutenant', 3]
]);

characters.sort((a, b) => {
  return rankOrder.get(a.rank) - rankOrder.get(b.rank);
});

// Picard, Riker, La Forge
characters;
```