---
title: JavaScript的this
date: 2023-02-11 17:30:06
categories:
- Mastering JS
tags:
- this
---

原文：[`this` in JavaScript](https://masteringjs.io/tutorials/fundamentals/this)

`this`关键字，也称为函数的“上下文”或“范围”，是一个强大的也令人迷惑的概念。本文帮你捋清思路。

<!-- more -->

[`this`关键字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this)可以引用函数的“执行上下文”。一个巧妙的说法是，`this`指向了调用函数所属的对象。

```javascript
// `this`是函数的隐性参数
const fn = function() {
  return this;
};

// 设置`this`的一种方法，是将函数附加到对象中
const obj1 = { fn };
const obj2 = { fn };

obj1.fn() === obj1; // true
obj1.fn() === obj2; // false

obj2.fn() === obj1; // false
obj2.fn() === obj2; // true
```

需要重点关注的是，JavaScript的函数也是普通的变量，`this`是可以改变的。一个常见的搞混`this`的场景：把函数赋给一个对象，但调用函数时不用对象调用。这种俗称函数[“丢失上下文”](https://www.freecodecamp.org/news/what-to-do-when-this-loses-context-f09664af076f/)。

```javascript
const fn = function() {
  return this;
};

const obj = { fn };

// 如果不用属性访问符`.`调用`fn()`
// 就是隐式的将函数上下文设为`null`
const myFn = obj.fn;
myFn() == null; // 严格模式下为true
```

简言之：`this`是函数调用时的隐性参数，当属于某个对象的函数调用时，`this`就是这个对象。

## [类](https://masteringjs.io/tutorials/fundamentals/class)

在ES6的类方法中，经常能见到`this`。在一个类方法中，`this`指向了调用方法所属的对象实例。

```javascript
class MyClass {
  myFunction() {
    return this;
  }
}

const obj = new MyClass();

obj.myFunction() === obj; // true
```

## 箭头函数

箭头函数不同于其他函数，它属于*词法上下文*。这意味着无论是以何种方式调用箭头函数，箭头函数中的`this`与箭头函数外的保持一致。

```javascript
const arrow = () => this;

arrow() == null; // true

const obj = { arrow };

// 即使`arrow()`绑定到一个对象
// 它依然与上下文有相同的`this`
obj.arrow() == null; // true
obj.arrow() == this; // true
```

## bind()、call()和apply()

每个JavaScript函数都有[`Function#call()`函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call)和[`Function#apply()`函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)，可以不必绑定对象就能指定`this`的值。`call()`和`apply()`将隐性参数`this`变成了显性参数。

另外[`Function#bind()`函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)可以创建一个预设`this`的函数副本。

```javascript
const fn = function() {
  return this;
};

const obj = {};

fn.call(obj) === obj; // true
fn.apply(obj) === obj; // true

const copy = fn.bind(obj);
copy() === obj; // true
copy === fn; // false
```
