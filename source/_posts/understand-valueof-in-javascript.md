---
title: 理解JavaScript中的valueOf()
date: 2023-01-03 22:53:50
categories:
- 前端
tags:
- valueOf
- JavaScript
---

原文：[Understand valueOf() in JavaScript](https://masteringjs.io/tutorials/fundamentals/valueof)

学习String类型、Number类型、Date类型都具备的valueOf()函数。

<!-- more -->

[valueOf()](https://www.w3schools.com/jsref/jsref_valueof_string.asp)函数是所有JavaScript对象中一个特殊的函数。根据[Mozilla文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/valueOf)，`valueOf()`函数“返回特定对象的原始值”，[JavaScript有六种原始类型](https://codeburst.io/javascript-data-types-explained-347555cd2d4d)：

* Boolean: true 或 false
* Number: -1, 0, 3.14, 等等
* String: '字符串'
* Null
* Undefined
* Symbol: Symbol('mySymbol')

valueOf()函数返回的值，使用`typeof`运算符后一定属于以上类型之一。以下是三种常见的JavaScript类型——[String](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/valueOf)、[Number](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/valueOf)和[Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/valueOf)——的`valueOf()`返回值。`String#valueOf()`和`Number#valueOf()`将对象进行“拆箱”，返回相应的原始字符串或数值。`Date#valueOf()`返回[JavaScript时间戳](https://masteringjs.io/tutorials/fundamentals/timestamps)。

```javascript
const s = new String('test');
typeof s; // 'object'
s.valueOf(); // 'test'
typeof s.valueOf(); // 'string'

const n = new Number(42);
n.valueOf(); // 42

const d = new Date('2019-06-01');
d.valueOf(); // 1559347200000
```

JavaScript在使用`==`（与`===`不同）、`<`、`>`、`<=`和`>=`这些运算符比较不同值时，调用`valueOf()`函数进行强制类型转换。例如，[JavaScript语言规范中在<运算符中使用valueOf的讨论](https://tc39.github.io/ecma262/#sec-abstract-relational-comparison)。

假设有一个对象，它的`valueOf()`始终返回0。在使用`<`和`==`运算符时，这个对象和原始数值0是等效的。然而，在使用`==`时，这个对象和`new Number(0)`不是等效的，因为JavaScript使用`==`比较两个对象时，**不会**调用`valueOf()`进行强制类型转换。

```
class MyClass {
  valueOf() {
    return 0;
  }
}

const obj = new MyClass();

// 使用 `<`, `>`, `>=` and `<=`, `obj` 等效于 0
obj < new Number(-1); // false
obj > new Number(-1); // true
obj < -1; // false
obj > -1; // true

// 使用 `==`, `obj` 等效于原始数值0
// 但不等效于Number对象0。因为 `obj` and `new Number(0)` 都是对象，JS不会调用 `valueOf()`
obj == new Number(0); // false
obj == 0; // true
0 == obj; // true

// `===` 不会调用 `valueOf()`。
obj === Number(0); // false
```