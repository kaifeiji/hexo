---
title: JavaScript对象的原型（prototype）简介
date: 2023-01-28 18:23:47
categories:
- Mastering JS
tags:
- prototype
---

原文：[Intro to Object Prototypes in JavaScript](https://masteringjs.io/tutorials/fundamentals/prototype)

JavaScript的继承是基于原型（prototype-based）的，即使使用ES6中类的`extends`关键词时也是如此。本文介绍了原型（prototype）的一些知识。

<!-- more -->

JavaScript中用`{}`创建一个空对象，它其实自带了一些内置属性，例如[`toString()`函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/toString)。

```javascript
const obj = {};
obj.toString(); // '[object Object]'
```

[Mozilla的文档把这个函数记录为`Object.prototype.toString()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/toString)，因为`obj`是`Object`类的一个实例。

在调用`toString`属性时，JavaScript先查看`obj`是否有`toString`属性，如果没有，JavaScript沿着继承链向上找到`Object.prototype`，继续查看`Object.prototype`是否有`toString`属性。

```javascript
const obj = {};
obj instanceof Object; // true
obj.toString === Object.prototype.toString; // true

obj.toString = () => {};
obj.toString === Object.prototype.toString; // false
```

可以把`Object.prototype`看作是一个模板对象，所有对象都从它这里继承一些方法和属性。

## 向prototype添加属性

prototype与JavaScript的其他对象没有本质区别。这意味着可以向`Object.prototype`添加新属性，这样所有对象都可以访问到这些属性。

```javascript
// 为所有对象添加 `getAnswer()` 函数
Object.prototype.getAnswer = function() { return 42 };

const obj = {};
obj.getAnswer(); // 42
```

虽然可以向`Object.prototype`添加方法，但这样做是不对的，这种操作会导致未来版本JavaScript的兼容性问题。例如，一个流行的库添加了一个`Array.prototype.flatten()`，与[一个新的JavaScript内置函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/flat)冲突，导致了[著名的“Smoosh门”崩溃事件](https://developers.google.com/web/updates/2018/03/smooshgate)。

## 创建自己的prototype原型

在ES6之前的类，只是一个用`new`调用的普通的传统函数。

```javascript
function MyClass() {}
```

`MyClass`函数有一个`prototype`属性，是可以修改的。

```javascript
function MyClass() {}

// 向`MyClass`所有实例添加`getAnswer()`函数
MyClass.prototype.getAnswer = function() { return 42; };

const obj = new MyClass();
obj.getAnswer(); // 42
```

甚至可以整体覆盖`MyClass`函数的`prototype`。

```javascript
function MyClass() {}

// 整体覆盖prototype
MyClass.prototype = {
  getAnswer: function() { return 42; }
};

const obj = new MyClass();
obj.getAnswer(); // 42
```

## 从其他类继承

`prototype`对象并不一定是一个普通的对象，它可以是任何其他类的实例。要创建一个`MyChildClass`类，继承`MyClass`，可以将`MyChildClass`的`prototype`设置为`MyClass`的一个实例。

```javascript
function MyClass() {}

// 整体覆盖prototype
MyClass.prototype = {
  getAnswer: function() { return 42; }
};

function MyChildClass() {}
MyChildClass.prototype = new MyClass();

const obj = new MyChildClass();
obj.getAnswer(); // 42

// `obj`是`MyChildClass`的实例，`MyChildClass`继承于`MyClass`，`MyClass`继承于`Object`。
obj instanceof MyChildClass; // true
obj instanceof MyClass; // true
obj instanceof Object; // true
```

`MyChildClass`继承于`MyClass`，`MyClass`继承于`Object`。因为`MyChildClass.prototype`是`MyClass`的实例，而`MyClass.prototype`是`Object`的实例。这就是JavaScript开发者常说的[原型链](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Inheritance_and_the_prototype_chain#Inheritance_with_the_prototype_chain)。

## 获取对象的原型

给定一个对象，可以用`.constructor.prototype`获取它的prototype。

```javascript
function MyClass() {}

const obj = new MyClass();
obj.constructor.prototype.getAnswer = function() { return 42; };

const obj2 = new MyClass();
obj2.getAnswer(); // 42
```

这是因为对象的`constructor`属性指向了`Object.prototype.constructor`。另外还有一个和`constructor.prototype`相似的，非标准的`__proto__`属性。

`constructor`和`__proto__`属性是潜在的[原型中毒](https://medium.com/intrinsic/javascript-prototype-poisoning-vulnerabilities-in-the-wild-7bc15347c96)攻击向量。一些流行的JavaScript库，包括[lodash](https://snyk.io/blog/snyk-research-team-discovers-severe-prototype-pollution-security-vulnerabilities-affecting-all-versions-of-lodash/)和[Mongoose](https://thecodebarbarian.com/mongoose-prototype-pollution-vulnerability-disclosure.html)，都曾报告过原型中毒缺陷。