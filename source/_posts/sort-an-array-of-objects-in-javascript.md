---
title: JavaScript对象数组排序
date: 2023-02-24 20:06:06
categories:
- Mastering JS
hide: true
tags:
- sort
---

JavaScript的内置`sort`函数可以用属性将对象数组进行排序。

<!-- more -->

有一个[JavaScript数组](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)，其中是《星际迷航：下一代》的演员表：

```javascript
const characters = [
  { firstName: 'Jean-Luc', lastName: 'Picard', rank: 'Captain', age: 59 },
  { firstName: 'Will', lastName: 'Riker', rank: 'Commander', age: 29 },
  { firstName: 'Geordi', lastName: 'La Forge', rank: 'Lieutenant', age: 29 }
];
```

如何按不同属性进行排序呢？

## 按age排序

[内置的`Array#sort()`函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)可以接收一个[回调](https://masteringjs.io/tutorials/fundamentals/callbacks)参数，用于比较数组中的两个元素。

回调函数`compareFunction()`接收2个参数`a`、`b`，如果`compareFunction(a, b)`返回小于`0`的值，JavaScript认为`a`比`b`小，如果返回大于`0`的值，认为`a`比`b`大。

这就很容易按数值属性排序，比如`age`。如果`a.age - b.age < 0`，`b`就比`a`要年龄大。

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

## 按lastName排序

按字符串属性排序也很简单，比如`lastName`。因为JavaScript的`<`和`>`可以比较字符串。按字符串属性排序，`compareFunction()`可以用`<`比较两个字符串：

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

## 按rank排序

按`rank`排序比较难一些，因为`rank`并非按字母表顺序，而JavaScript不知道Captain比Lieutenant级别高。

要按这种自定义顺序排序，要定义`rank`到数值的映射，这样更容易比较。

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
