---
title: JavaScript中in和hasOwnProperty的区别
date: 2023-01-06 23:10:57
categories:
- Mastering JS
tags:
- JavaScript
- hasOwnProperty
---

JavaScript中检查对象中是否包含指定key的两种方法。

<!-- more -->

一个普通的JavaScript对象，由两种常见的方法检查对象中是否包含指定的key：运算符[in](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/in)和[hasOwnProperty()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/hasOwnProperty)函数。对于简单的没有特殊key的POJO而言，二者是等效的：

```javascript
const obj = { answer: 42 };
'answer' in obj; // true
obj.hasOwnProperty('answer'); // true

'does not exist' in obj; // false
obj.hasOwnProperty('does not exist'); // false
```

二者也都支持[ES6的symbol](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol)。

```javascript
const symbol = Symbol('answer');
const obj = { [symbol]: 42 };

symbol in obj; // true
obj.hasOwnProperty(symbol); // true
```

二者的区别是什么呢？主要区别就是`in`对继承属性返回`true`，而`hasOwnProperty()`对继承属性返回`false`。

例如，JavaScript的`Object`基类有一个[`__proto__`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/proto)属性，一个[`constructor`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/constructor)属性，和一个[`hasOwnProperty`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/hasOwnProperty)函数。运算符`in`对这些属性会返回`true`，而`hasOwnProperty()`返回`false`。

```javascript
'constructor' in obj; // true
'__proto__' in obj; // true
'hasOwnProperty' in obj; // true

obj.hasOwnProperty('constructor'); // false
obj.hasOwnProperty('__proto__'); // false
obj.hasOwnProperty('hasOwnProperty'); // false
```

因为`hasOwnProperty()`会忽略继承属性，它对POJO（plain old JavaScript object，普通的传统JavaScript对象）而言是更好的选择。但对于ES6类的getter和方法，`hasOwnProperty()`会返回`false`，见[ES6 getters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get)。

```javascript
class BaseClass {
  get baseProp() {
    return 42;
  }
}
class ChildClass extends BaseClass {
  get childProp() {
    return 42;
  }
}
const base = new BaseClass();
const child = new ChildClass();

'baseProp' in base; // true
'childProp' in child; // true
'baseProp' in child; // true

base.hasOwnProperty('baseProp'); // false
child.hasOwnProperty('childProp'); // false
child.hasOwnProperty('baseProp'); // false
```

以下是`in`和`hasOwnProperty()`的对比表。

![](/post-images/the-difference-between-in-and-hasownproperty-in-javascript-2023-01-06-23-38-01.png)

总之，`hasOwnProperty()`大多数场景是正确的选择，因为需要避免特殊key问题，比如`constructor`。一个好的经验法则：如果检查一个对象是否包含某个属性，应该使用`hasOwnProperty()`，如果检查一个对象是否包含需要调用的函数，例如`toString()`，应该用`in`。