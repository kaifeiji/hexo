---
title: JavaScript中的Object.assign()
date: 2023-01-26 09:28:41
categories:
- Mastering JS
tags:
- Object.assign
---

原文：[Object.assign() in JavaScript](https://masteringjs.io/tutorials/fundamentals/assign)

`Object.assign()`函数将一个对象的属性赋给另一个对象，可以用于浅拷贝对象或一次性赋值多个属性。

<!-- more -->

JavaScript的[`Object.assign()`函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)从一个或多个来源对象复制属性到目标对象，它的返回值是目标对象。

```javascript
const source = { hello: 'world' };
const target = {};

// 第一个参数是目标对象，之后所有参数是来源对象。
const ret = Object.assign(target, source);

// `Object.assign()` 会修改 `target`，然后返回 `target`
ret === target; // true
target.hello; // 'World'
```

虽然[展开运算符`...`做浅拷贝一般比Object.assign()更快](https://thecodebarbarian.com/object-assign-vs-object-spread.html)，但Object.assign()也经常用于浅拷贝对象。例如在[Redux的recucer](https://redux.js.org/recipes/using-object-spread-operator)中就经常需要进行浅拷贝。

```javascript
const obj = { name: 'Jean-Luc Picard', age: 59 };

// `Object.assign({}, obj)` 常用于生成 `obj` 的浅拷贝。
const shallowCopy = Object.assign({}, obj);

shallowCopy === obj; // false

// 修改拷贝对象 `shallowCopy` 不影响 `obj`
shallowCopy.rank = 'Captain';
obj.rank; // undefined
```

## 多个源对象

[`Object.assign()`可以传入多个源对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign#Merging_objects_with_same_properties)，如果多个源对象中有相同的属性，以最后一个的属性为准。

```javascript
const o1 = { a: 1, b: 1, c: 1 };
const o2 = { a: 2, b: 2 };
const o3 = { a: 3 };

Object.assign(o1, o2, o3); // { a: 3, b: 2, c: 1 }
```