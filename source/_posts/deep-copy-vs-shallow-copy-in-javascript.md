---
title: JavaScript的深拷贝和浅拷贝
date: 2023-01-27 19:44:00
categories:
- Mastering JS
tags:
- 深拷贝
- 浅拷贝
---

原文：[Deep Copy vs Shallow Copy in JavaScript](https://masteringjs.io/tutorials/fundamentals/shallow-copy)

在JavaScript中，经常会听说“深克隆”和“浅克隆”，也叫做浅拷贝和浅拷贝。本文介绍了其含义及示例。

<!-- more -->

[复制一个JavaScript的对象](https://scotch.io/bar-talk/copying-objects-in-javascript)，可以是深拷贝或浅拷贝。其中的不同之处在于，深拷贝会递归拷贝*所有*的嵌套对象，比如[lodash的`cloneDeep()`函数](https://lodash.com/docs/4.17.15#cloneDeep)。[`Object.assign()`函数](https://masteringjs.io/tutorials/fundamentals/assign)可以浅拷贝对象。

```javascript
const obj = { answer: 42 };
// `Object.assign()` 函数常用于浅拷贝对象
const copy = Object.assign({}, obj);

++copy.answer; // 43
obj.answer; // 42, 不变, 因为 `copy` 是 `obj` 的拷贝
```

浅拷贝**不会**复制嵌套对象，如果`obj`包含一个对象属性`name`，`Object.assign()`会保持`name`原有的引用，而不是创建一个`name`的拷贝。

```javascript
const obj = { name: { first: 'Jean-Luc', last: 'Picard' } };
const copy = Object.assign({}, obj);

copy.name.first = 'Johnny';
obj.name.first; // 'Johnny', `name` **没有** 克隆
```

一般来说，深拷贝对象需要类似lodash的库。另外也可以用`JSON.stringify()`序列化对象，然后用`JSON.parse()`解析成对象。

```javascript
const obj = { name: { first: 'Jean-Luc', last: 'Picard' } };
const copy = JSON.parse(JSON.stringify(obj));

copy.name.first = 'Johnny';
obj.name.first; // 'Jean-Luc'
```

但是这种方法适用于只包含字符串、数值、布尔值、对象和数组的对象。例如，对象中有一个`date`属性是JavaScript的日期类型，拷贝对象的`date`属性会变成字符串，因为`JSON.stringify()`序列化日期会转换成字符串。

```javascript
const obj = { date: new Date('2019-06-01') };
const copy = JSON.parse(JSON.stringify(obj));

obj.date instanceof Date; // true
copy.date instanceof Date; // false, `date` 是字符串
```