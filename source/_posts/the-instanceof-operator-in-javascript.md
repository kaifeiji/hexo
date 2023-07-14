---
title: JavaScript的instanceof操作符
date: 2023-02-01 21:34:59
categories:
- Mastering JS
hide: true
tags:
- instanceof
---

原文：[The instanceof Operator in JavaScript](https://masteringjs.io/tutorials/fundamentals/instanceof)

`instanceof`运算符可以确定一个对象是否是一个类的实例。

<!-- more -->

[`instanceof`运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/instanceof)可以判断一个对象是否是某个[JavaScript类](https://masteringjs.io/tutorials/fundamentals/class)的实例。

```javascript
class Rectangle {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
}

const obj = new Rectangle(3, 5);
obj.height; // 3
obj.width; // 5

// `instanceof` 关键字判断一个对象是否由特定类创建
obj instanceof Rectangle; // true
({}) instanceof Rectangle; // false
```

技术上来讲，`instanceof`运算符检查对象的[原型链](https://masteringjs.io/tutorials/fundamentals/prototype#inheriting-from-another-class)，看原型链上有没有`constructor`等于给定的类。这样，如果存在[继承关系](https://masteringjs.io/tutorials/fundamentals/tutorials/fundamentals/class#inheritance)，子类的实例同样也是基类的一个实例。

```javascript
class BaseClass {}
class SubClass extends BaseClass {}

const obj = new SubClass();

obj instanceof SubClass; // true
obj instanceof BaseClass; // true
```

## Object类

[`Object`类](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)是所有JavaScript类的基类。

```javascript
class MyClass {}

const obj = new MyClass();

obj instanceof Object; // true
({}) instanceof Object; // true
null instanceof Object; // false
```

你可能会想到用`v instanceof Object`检查`v`是否是对象。在大部分场景是可行的，但[在某些情况下对象不是Object的实例](https://2ality.com/2012/08/instanceof-object.html)。

```javascript
// `Object.prototype` 从技术上来说不是 `Object` 的实例
// 因为原型链总是要结束的
// 译注：Object.prototype是原型链的终点不再向上追溯
typeof Object.prototype; // 'object'
Object.prototype instanceof Object; // false

// 将函数的`prototype`设置为`Object.create(null)`
// `Object`不在对象的原型链中
function MyClass() {}
MyClass.prototype = Object.create(null);

typeof new MyClass(); // 'object'
(new MyClass()) instanceof Object; // false
```

## 异常情况

`instanceof`运算符在右侧不是函数时会抛出错误。

*译注：函数的判断依据是`typeof a === 'function'`，ES6的类也是函数*

```javascript
class MyClass {}

function MyOtherClass() {}

const obj = {};

obj instanceof MyClass; // false
obj instanceof MyOtherClass; // false

// Throws "TypeError: Right-hand side of 'instanceof' is not callable"
obj instanceof { answer: 42 };
```