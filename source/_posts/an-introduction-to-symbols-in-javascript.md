---
title: 介绍JavaScript中Symbol
date: 2023-07-27 16:59:15
categories:
- Mastering JS
hide: true
tags:
- Symbol
---

原文：[An Introduction to Symbols in JavaScript](https://masteringjs.io/tutorials/fundamentals/symbol)

Symbol提供了一种在对象中创建隐藏属性的方式。

<!-- more -->

[Symbol](http://thecodebarbarian.com/a-practical-guide-to-symbols-in-javascript.html)和`number`、`boolean`、`null`一样，是JavaScript中一种原始数据类型，常被用于避免属性名冲突或模拟对象私有属性。

调用全局函数`Symbol()`可以创建一个Symbol：

```javascript
const sym = Symbol();
```

`Symbol()`函数接收一个字符串参数`description`，在打印Symbol时会显示出来。

```javascript
const sym = Symbol('my description');

console.log(sym); // Prints "Symbol(my description)"
```

## 主要特性

Symbol有两个主要特性。其一是**任意两个Symbol都不相等**，即使有相同的`description`参数，也不相等。

```javascript
Symbol() === Symbol(); // false

Symbol('test') === Symbol('test'); // false
```

其二是**对象的属性名可以用Symbol**。通常，对象的属性名要么是Symbol要么是字符串。

```javascript
const test = Symbol('test');

const obj = {};
obj.test = 'hello';
obj[test] = 'world';

obj.test; // 'hello'
obj[test]; // 'world'
```

由于两个Symbol必然不相等，那么除非可以访问到Symbol，否则一段代码是无法访问Symbol所代表的属性的。这样，用Symbol可以很方便的创建隐藏属性，只能在特定的函数中进行访问。

```javascript
function addSymbol(obj) {
  const sym = Symbol('test');
  obj[sym] = 'my hidden value';

  return obj;
}

const obj = addSymbol({});
// 在此处无法直接访问obj[sym]，除非使用`Object.getOwnPropertySymbols()`
```

Symbol在`JSON.stringify()`的输出中也会被排除掉，非常适合存储只在编码过程中需要用到，而终端用户不会看到信息。

*本教程对您有帮助吗？来[GitHub仓库](https://github.com/mastering-js/masteringjs.io)点个星支持我们吧！*
