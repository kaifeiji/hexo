---
title: JavaScript如何比较两个Object
date: 2022-12-30 17:44:43
categories:
- 前端
tags:
- JavaScript
- Object
- Compare
- 对象
- 比较
---

原文：[How to Compare Objects in JavaScript](https://masteringjs.io/tutorials/fundamentals/compare-objects)

JavaScript中有很多方法来比较对象。本文提供了JavaScript比较对象的三种方法，以及它们的优缺点。

<!-- more -->

在JavaScript中，对象是引用类型。这意味着，只有两个变量指向内存中的同一个对象时，它们才[严格相等](https://masteringjs.io/tutorials/fundamentals/equality)。

```javascript
const o1 = { answer: 42 };
const o2 = o1;
const o3 = { answer: 42 };

o1 === o2; // true, 相同引用
o1 === o3; // false, 相同的key、value，但引用不同
```

然而，如果你想检查两个[POJO](https://masteringjs.io/tutorials/fundamentals/pojo)是否包含相同的数据呢？换种说法，就是具有相同的key和value？这里提供了3种方法。

## key和value的浅层相等

一种简单的方法，遍历两个对象中的每个key和value，检查其是否严格相等。

```javascript
const o1 = { question: null, answer: 42 };
const o2 = { question: null, answer: 42 };

objectsEqual(o1, o2); // true
objectsEqual(o1, { answer: 43 }); // false

function objectsEqual(o1, o2) {
  const entries1 = Object.entries(o1);
  const entries2 = Object.entries(o2);
  if (entries1.length !== entries2.length) {
    return false;
  }
  for (let i = 0; i < entries1.length; ++i) {
    // Keys
    if (entries1[i][0] !== entries2[i][0]) {
      return false;
    }
    // Values
    if (entries1[i][1] !== entries2[i][1]) {
      return false;
    }
  }

  return true;
}
```

## 使用JSON.stringify()判断深层相等

上一种方法，是通过检查key和value是否严格相等来比较两个对象。但是如果其中一个value是对象怎么办？

```javascript
const o1 = { name: { first: 'Arthur', lastName: 'Dent' }, planet: 'Earth' };
const o2 = { name: { first: 'Arthur', lastName: 'Dent' }, planet: 'Earth' };

objectsEqual(o1, o2); // false, 因为 `o1.name !== o2.name`
```

你可以改造`objectsEqual()`，把它变成递归的，但是就必须小心无限递归问题。另一种比较两个对象是否深层相等的方法，就是用[JSON.stringify()](https://masteringjs.io/tutorials/fundamentals/stringify)比较它们的JSON字符串：

```javascript
const o1 = { name: { first: 'Arthur', lastName: 'Dent' }, planet: 'Earth' };
const o2 = { name: { first: 'Arthur', lastName: 'Dent' }, planet: 'Earth' };

JSON.stringify(o1) === JSON.stringify(o2); // true

delete o2.planet;
JSON.stringify(o1) === JSON.stringify(o2); // false
```

JSON.stringify()有一些局限性，它并不是一个好的方案。首先，key的顺序问题：

```javascript
const o1 = { question: null, answer: 42 };
const o2 = { answer: 42, question: null };
JSON.stringify(o1) === JSON.stringify(o2); // false
```

其次，JSON并不能表达所有的JavaScript类型。JSON.stringify()会将日期类型转换为字符串，而且忽略value为undefined的key，这会导致一些意外情况发生。

```javascript
const o1 = { myDate: new Date('2016-06-01'), otherProperty: undefined };
const o2 = { myDate: '2016-01-01T00:00:00.000Z' };

JSON.stringify(o1) === JSON.stringify(o2); // true
```

## 使用Lodash的isEqual()

[Lodash的isEqual()函数](https://lodash.com/docs/4.17.15#isEqual)是比较两个对象的最成熟的方案。它充分考虑并处理了许多边界情况，避免了前两种方法的缺陷。

```javascript
const obj1 = {
  date: new Date('2020/06/01'),
  num: new Number(1)
};
const obj2 = {
  date: new Date('2020/06/01'),
  num: 1
};

_.isEqual(obj1, obj2); // true
const obj1 = { name: 'Will Riker', rank: 'Commander' };

class Character {}
const obj2 = new Character();
Object.assign(obj2, { name: 'Will Riker', rank: 'Commander' });

_.isEqual(obj1, obj2); // false
```

`isEqual()`函数也巧妙的避免了无限递归问题。

```javascript
const obj1 = {};
const obj2 = {};

obj1.circular = obj1;
obj2.circular = obj1;

_.isEqual(obj1, obj2); // true
```

如果你已经在项目中使用Lodash了，那么`isEqual()`就是比较两个对象是否深层相等的最佳方案。在一些不包含value为对象的情况下，浅层比较方案也可以满足使用。`JSON.stringify()`也可以在不使用Lodash时提供粗略的深层相等检查。但是，如果已经使用了Lodash，`isEqual()`是检查两个对象是否深层相等的最佳方案。