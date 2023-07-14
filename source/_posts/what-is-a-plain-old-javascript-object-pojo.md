---
title: POJO（Plain Old JavaScript Object）是什么
date: 2023-02-03 16:45:47
categories:
- Mastering JS
hide: true
tags:
- POJO
---

原文：[What is a Plain Old JavaScript Object (POJO)?](https://masteringjs.io/tutorials/fundamentals/pojo)

POJO是JavaScript存储用户输入数据最常用的方式，如何确切定义POJO呢？

<!-- more -->

关于JavaScript的POJO是什么有很多讨论：[StackOverflow上有人认为任何包含用户数据的类都是POJO](https://stackoverflow.com/questions/49630228/create-a-model-pojo-in-javascript)，而[一个流行的npm包](https://github.com/bttmly/is-pojo)把POJO定义为原型是`Object.prototype`的任何对象。

直观的看待POJO，它其实是只包含数据的对象，没有方法和内部状态。大部分JavaScript代码将用花括号`{}`创建的对象视为POJO。而有些更严格的代码会[用`Object.create(null)`来创建POJO，避免从内置的`Object`类继承其他属性](https://davidwalsh.name/object-create-null)。

```javascript
// 如果用`{}`创建对象`obj`，`obj`是`Object`类的实例
// 它有一些内置的属性
let obj = {};
obj.hasOwnProperty; // [Function]
obj.constructor === Object; // true

// 另一种方式，`Object.create(null)`创建的对象
// 不从**任何**类继承
obj = Object.create(null);
typeof obj; // 'object'
obj.hasOwnProperty; // undefined
obj.constructor; // undefined

obj.prop = 42;
obj.prop; // 42
```

## POJO和Map

[JavaScript的`Map`](http://thecodebarbarian.com/the-80-20-guide-to-maps-in-javascript.html)可以替代POJO用于存储数据，因为它不从`Objec`t类继承属性。然而对象比`Map`更容易操作，因为并非所有的JavaScript函数、框架和库都支持`Map`。例如[`JSON.stringify()`函数](http://thecodebarbarian.com/the-80-20-guide-to-json-stringify-in-javascript)不支持序列化`Map`。

```javascript
const map = new Map([['answer', 42]]);
JSON.stringify(map); // '{}'
```

## 检查对象是否POJO

检查一个对象是否是POJO有一些棘手，还要考虑`Object.create(null)`创建的对象是不是POJO。最安全的方法是用[`Object.getPrototypeOf()`函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/getPrototypeOf)比较对象的原型。

```javascript
function isPOJO(arg) {
  if (arg == null || typeof arg !== 'object') {
    return false;
  }
  const proto = Object.getPrototypeOf(arg);
  if (proto == null) {
    return true; // `Object.create(null)`
  }
  return proto === Object.prototype;
}

isPOJO({}); // true
isPOJO(Object.create(null)); // true
isPOJO(null); // false
isPOJO(new Number(42)); // false
```

例如，以下是[Mongoose内部的`isPOJO()`函数](https://github.com/Automattic/mongoose/blob/43b63ae8d18e49db3ddb56b4c843637339495a76/lib/utils.js#L505-L514)：

```javascript
exports.isPOJO = function isPOJO(arg) {
  if (arg == null || typeof arg !== 'object') {
    return false;
  }
  const proto = Object.getPrototypeOf(arg);
  // `Object.create(null)`创建的对象prototype是null
  // 检查`proto`的constructor是安全的
  // 因为`getPrototypeOf()`已经打破了对象数据和对象元数据的边界
  return !proto || proto.constructor.name === 'Object';
};
```

Mongoose没有用`proto.constructor === Object`，而是检查`constructor.name`属性，这样可以支持[Node.js的`vm`模块](https://www.w3schools.com/nodejs/ref_vm.asp)生成的对象。

```javascript
const obj = require('vm').runInNewContext('({})');
// `obj`继承于不同JavaScript上下文的`Object`类
obj.constructor === Object; // false
obj.constructor.name; // 'Object'
```