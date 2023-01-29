---
title: JavaScript的Object.seal()
date: 2023-01-29 18:32:38
categories:
- Mastering JS
tags:
- Object.seal
---

原文：[Object.seal() in JavaScript](https://masteringjs.io/tutorials/fundamentals/seal)

`seal()`函数可以防止对象添加或移除属性，同时仍然允许修改已有的属性，本文介绍了一些相关知识。

<!-- more -->

[`Object.seal()`函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze)可以防止JavaScript对象添加、移除或重新配置属性。

```javascript
const sealed = Object.seal({ answer: 42 });

sealed.answer = 43; // OK

// TypeError: Cannot delete property 'answer' of #<Object>
delete sealed.answer;

// TypeError: Cannot add property newProp, object is not extensible
sealed.newProp = 42;

// TypeError: Cannot redefine property: answer
Object.defineProperty(sealed, 'answer', { enumerable: false });
```

`seal()`和[`Object.freeze()`函数](https://masteringjs.io/tutorials/fundamentals/freeze)有些相似，关键不同在于：`seal`后的对象仍然可以编辑已有属性，但`freeze`后的对象不能编辑。

```javascript
const sealed = Object.seal({ answer: 42 });
const frozen = Object.freeze({ answer: 42 });

sealed.answer = 43; // ok
// TypeError: Cannot assign to read only property 'answer' of object '#<Object>'
frozen.answer = 43;
```

和`freeze()`类似，在[严格模式](https://masteringjs.io/tutorials/fundamentals/strict)下`seal`后的对象添加、移除或重新配置属性会抛出异常，而非严格模式只是失败但不报错。

`seal()`函数也有些类似于[`Object.preventExtensions()`函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/preventExtensions)，但后者只是防止向对象添加新属性，仍然可以移除或重新配置已有属性。

## 何时使用seal()

生产环境中，`freeze()`函数更常用一些，`seal()`函数很少见。可能会用到`seal()`的一个场景是对Node.js中`global`对象的保护。

```javascript
Object.seal(global);

global.newProp = 42; // TypeError
```

一些npm包，例如[safe-buffer](https://www.npmjs.com/package/safe-buffer)，有意的修改了已有的global中的变量，但要防止其他npm包无意间添加新的`global`变量。诚然对`global`使用`seal()`在Node.js社区中并不常见，当然也不是既定的最佳实践，但可以试试——有惊喜（译注：没发现有啥惊喜）。