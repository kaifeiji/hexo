---
title: JavaScript比较字符串
date: 2023-02-26 23:24:15
categories:
tags:
---

原文：[Compare Strings in JavaScript](https://masteringjs.io/tutorials/fundamentals/compare-strings)

JavaScript的比较运算符很容易比较字符串，另外还有`localeCompare()`可以用于排序。

<!-- more -->

JavaScript中，两个字符串相等代表什么？一个字符串大于另一个又代表什么？

JavaScript的[三等运算符`===`](https://masteringjs.io/tutorials/fundamentals/equals)，在两个字符串完全相同时返回`true`，否则返回`false`：

```javascript
'hello' === 'hello'; // true
'Hello' === 'hello'; // false
```

`<`和`>`运算符按[词法顺序](https://en.wikipedia.org/wiki/Lexicographical_order)比较字符串。词法顺序基本上等同于“字典顺序”。简单的说，如果按[ASCII表](https://en.wikipedia.org/wiki/ASCII)顺序，`a`的第一个字符在`b`的第一个字符之前，那么`a < b`；如果`a`和`b`的第一个字符相等，而`a.slice(1) < b.slice(1)`，那么也是`a < b`。

```javascript
'alpha' < 'zeta'; // true, 因为'a'在'z'之前
'alpha' < 'Zeta'; // false, 因为ASCII表中 'Z' = 90, 'a' = 97
'one' < '1'; // false, 因为ASCII表中 '1' = 49, 'o' = 111

'octo' < 'okto'; // true, 因为 'c' = 99, 'k' = 107
'a' < 'alpha'; // true, 因为ASCII表中字符串终止符 = '0'
```

## 字符串数组排序

JavaScript的[`Array#sort()`函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)按词法顺序自动排序。

```javascript
['Zeta', '1', 'alpha', ''].sort(); // ['', '1', 'Zeta', 'alpha']
```

[lodash的`sortBy()`函数](https://masteringjs.io/tutorials/lodash/sortby)对字符串数组也默认按词法顺序排序。

```javascript
_.sortBy(['Zeta', '1', 'alpha', '']); // ['', '1', 'Zeta', 'alpha']
```

[一个对象数组，如何按字符串属性排序](https://masteringjs.io/tutorials/fundamentals/sort-array-of-objects)？`Array#sort()`接收一个[回调函数](https://masteringjs.io/tutorials/fundamentals/callbacks)作为参数，回调接收2个值`a`和`b`，返回值如下：

* 0 如果a和b相等
* 正数 如果a > b
* 负数 如果a < b

给定一个对象数组，对象中包含`lastName`属性，以下是按`lastName`对数组排序的一种方法。

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

更简单的方法是使用JavaScript内置的[`localeCompare()`函数](https://www.w3schools.com/jsref/jsref_localecompare.asp)。调用`a.localeCompare(b)`，如果`a < b`，返回`-1`，如果`a === b`，返回`0`，如果`a > b`，返回`1`。

```javascript
const characters = [
  { firstName: 'Jean-Luc', lastName: 'Picard', rank: 'Captain', age: 59 },
  { firstName: 'Will', lastName: 'Riker', rank: 'Commander', age: 29 },
  { firstName: 'Geordi', lastName: 'La Forge', rank: 'Lieutenant', age: 29 }
];

characters.sort((a, b) => a.lastName.localeCompare(b.lastName));

// La Forge, Picard, Riker
characters;
```
