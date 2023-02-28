---
title: JavaScript比较数组
date: 2023-02-28 22:55:51
categories:
- Mastering JS
tags:
- 数组
---

原文：[Compare Arrays in JavaScript](https://masteringjs.io/tutorials/fundamentals/compare-arrays)

JavaScript比较两个数组的一些技巧。

<!-- more -->

JavaScript中的数组本质是对象，因此用[`===`](https://masteringjs.io/tutorials/fundamentals/equals)比较，只有在数组是相同引用时才会返回`true`。

```javascript
const a = [1, 2, 3];
const b = [1, 2, 3];

a === a; // true
a === b; // false
```

如何判断两个数组相等？相等是个棘手的问题：JavaScript标准定义了[4种不同的方法，用于检查两个值是否“相等”](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Equality_comparisons_and_sameness)，但都没有考虑到深入对象内部的相等。

在某些场景下，你需要尽可能精确的定义“相等”。在软件工程中，提出正确的问题往往能够得到明显的答案。

基于这些原则，本文提供了3种数组相等的定义，以及如何判断相等。

## 相同长度、每个值相等

比较`a`和`b`的一种方式，是检查`a`的每个值是否严格等于`b`的对应值。当数组元素都是原始类型而非对象时很容易实现的。

```javascript
const a = [1, 2, 3];
const b = [4, 5, 6];
const c = [1, 2, 3];

function arrayEquals(a, b) {
  return Array.isArray(a) &&
    Array.isArray(b) &&
    a.length === b.length &&
    a.every((val, index) => val === b[index]);
}

arrayEquals(a, b); // false
arrayEquals(a, c); // true
```

## POJO的深度相等

上文的`arrayEquals()`函数对原始类型值运行正常，但想要比较对象数组就麻烦了。

```javascript
const a = [{ answer: 42 }, { powerLevel: 9001 }];
const b = [{ answer: 42 }, { powerLevel: 9001 }];

// false, 因为 { answer: 42 } !== { answer: 42 }, 引用不同
arrayEquals(a, b);
```

考虑到对象数组的比较，一种简单的方式是用[`JSON.stringify()`](https://thecodebarbarian.com/the-80-20-guide-to-json-stringify-in-javascript.html)。

```javascript
const a = [{ answer: 42 }, { powerLevel: 9001 }];
const b = [{ answer: 42 }, { powerLevel: 9001 }];
const c = [{ answer: 42 }, { password: 'taco' }];

JSON.stringify(a) === JSON.stringify(b); // true
JSON.stringify(a) === JSON.stringify(c); // false
```

这种方式很方便，它需要很少的代码而且不会引用到外部库。但是比较`JSON.stringify()`的输出，根据使用的不同，边界情况下可能会发生意外。由于`undefined`不是有效的JSON值，以下数组具有相同的`JSON.stringify()`输出，因为`JSON.stringify()`将`undefined`转换为`null`。

```javascript
const a = [undefined];
const b = [null];
```

## 使用lodash的isEqual()

除了`null`和`undefined`的问题，比较`JSON.stringify()`的输出也没有考虑对象类型。一个对象，带有一个返回`42`的`toJSON()`函数，它的`JSON.stringify()`输出和数值`42`完全相同。

```javascript
const a = [{ toJSON: () => 42 }];
const b = [42];

JSON.stringify(a); // '[42]'
JSON.stringify(b); // '[42]'
```

相似的，自定义对象和POJO的`JSON.stringify()`也是相同的：

```javascript
class MyClass {
  constructor(obj) {
    Object.assign(this, obj);
  }
}

const a = [new MyClass({ answer: 42 })];
const b = [{ answer: 42 }];

JSON.stringify(a) === JSON.stringify(b); // true
```

与`JSON.stringify()`不同，[lodash的`isEqual()`函数](https://lodash.com/docs/4.17.15#isEqual)考虑了以上所有情况。

```javascript
const _ = require('lodash');

class MyClass {
  constructor(obj) {
    Object.assign(this, obj);
  }
}

const a = [new MyClass({ answer: 42 })];
const b = [{ answer: 42 }];

_.isEqual(a, b); // false
```

如果你需要比较类的对象或者其他花里胡哨的东西，lodash的`isEqual()`函数是正确的方式。`JSON.stringify()`对POJO而言是可用的，但是一定要考虑到`undefined`和`null`，而且只对可信的数据使用——`toJSON()`可能造成安全漏洞。
