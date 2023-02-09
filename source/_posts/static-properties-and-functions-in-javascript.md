---
title: JavaScript的静态属性和函数
date: 2023-02-09 21:44:34
categories:
- Mastering JS
tags:
- static
---

原文：[Static Properties and Functions in JavaScript](https://masteringjs.io/tutorials/fundamentals/static)

`static`关键字可以在ES6的类中定义静态属性和函数。

<!-- more -->

在[ES6的类](https://masteringjs.io/tutorials/fundamentals/class)中，`static`关键字可以定义类自身的函数，而非实例的函数。

```javascript
class MyClass {
  static myFunction() {
    return 42;
  }
}

typeof MyClass.myFunction; // 'function'
MyClass.myFunction(); // 42

// `myFunction()`是`MyClass`的函数
// **不是**`MyClass`实例的函数
const obj = new MyClass();
obj.myFunction; // undefined
```

在JavaScript中，类和其他对象没有什么不同。`static`可以在类内部定义类的函数，直接把函数赋值给`MyClass`也是等价的：

```javascript
class MyClass {}
MyClass.myFunction = function() {
  return 42;
};

MyClass.myFunction(); // 42
```

## this

在`static`函数中，`this`指向了类本身。

```javascript
class MyClass {
  static myFunction() {
    return this;
  }
}

MyClass.myFunction() === MyClass; // true
```

## 静态属性

[静态属性，也称为类字段](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes/Class_fields)，目前初于[TC39提案的第三阶段](https://github.com/tc39/proposals#stage-3)，这意味着它还不是JavaScript语言的一部分。但已经在最近版本的Chrome得到了支持。

*译注：本文写于2019年12月18日，译稿已是2023年，静态属性已成为JavaScript语言标准*

```javascript
class MyClass {
  static answer = 42;
}

MyClass.answer; // 42
```

在使用静态属性时，对于非原始类型要特别小心。如果[继承了非原始类型的静态属性](https://thecodebarbarian.com/static-properties-in-javascript-with-inheritance)，每个类继承的其实是同一个对象。

```javascript
class MyClass {
  static val = new Object();
}

class MyChildClass extends MyClass {}

MyChildClass.val === MyClass.val; // true
```
