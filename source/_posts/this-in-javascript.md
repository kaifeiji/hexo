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

`this`关键字可以引用函数的“执行上下文”。一个巧妙的说法是，`this`指向了调用函数时，函数所属的对象。

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

The important thing to note is that, since functions are plain old variables in JavaScript, this may change. One common way to mess up the value of this is to assign a function to an object and call the function without an associated object. This is informally known as the function losing its context.

const fn = function() {
  return this;
};

const obj = { fn };

// If you call `fn()` without a property access `.`, you're
// implicitly setting the function context to `null`.
const myFn = obj.fn;
myFn() == null; // true in strict mode
TLDR: this is an implicit parameter to a function call. It contains whatever object the function was a property of when it was called.

With Classes
You will often see this in ES6 class methods. In a class method, this refers to the instance of the object the method is called on.

class MyClass {
  myFunction() {
    return this;
  }
}

const obj = new MyClass();

obj.myFunction() === obj; // true
Arrow Functions
Arrow functions are special because, unlike other functions, they have lexical context. That means this in an arrow function is the same as this outside the arrow function, regardless of how you call the arrow function.

const arrow = () => this;

arrow() == null; // true

const obj = { arrow };

// Even though `arrow()` is attached to an object, it still
// has the same context as the surrounding block.
obj.arrow() == null; // true
obj.arrow() == this; // true
Using bind(), call(), and apply()
Every JavaScript function has a Function#call() function and a Function#apply() function that lets you set the value of this without explicitly attaching the function to an object. You can think of call() and apply() as letting you set the implicit parameter this explicitly.

There is also a Function#bind() function that creates a copy of the function with a pre-set context.

const fn = function() {
  return this;
};

const obj = {};

fn.call(obj) === obj; // true
fn.apply(obj) === obj; // true

const copy = fn.bind(obj);
copy() === obj; // true
copy === fn; // false