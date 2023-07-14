---
title: JavaScript的typeof运算符
date: 2023-02-25 17:00:12
categories:
- Mastering JS
hide: true
tags:
- typeof
---

原文：[The `typeof` Operator in JavaScript](https://masteringjs.io/tutorials/fundamentals/typeof)

typeof运算符可以判断一个值的类型——number、string、object等。

<!-- more -->

[`typeof`运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/typeof)返回变量的类型。

```javascript
typeof 42; // 'number'
typeof 'test'; // 'string'
typeof true; // 'boolean'
typeof (void 0); // 'undefined'
typeof BigInt('1234'); // 'bigint'
typeof Symbol('foo'); // 'symbol'
typeof ({ answer: 42 }); // 'object'
typeof function() {}; // 'function'

// 对`typeof`而言，所有的对象都返回object
class MyClass {}
typeof (new MyClass()); // 'object'
```

一般而言：`typeof`运算符返回给定的值是[JavaScript的8种数据类型](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures)的哪一种。有一个很关键的例外：`null`。

## null

`typeof`一个很容易出错的地方，就是`typeof null === 'object'`。[这种情况有一些历史原因](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/typeof#null)，修正这一问题的提案也被否决了，目前看起来JavaScript是无法摆脱这个奇怪的语法了。

用`typeof`检查一个值是否真的是对象的方法，可以判断一下它的类型是不是`'object'`，它是否[严格不等于](https://masteringjs.io/tutorials/fundamentals/equals)`null`。

```javascript
function isObject(v) {
  return typeof v === 'object' && v !== null;
}
```

## 错误场景

对未定义的[块级作用域变量](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let)使用`typeof`运算符，会抛出错误。

```javascript
// 抛出错误 'ReferenceError: v is not defined'
console.log(typeof v);

let v;
```

这个错误只会在块级作用域变量出现。例如，如果完全不定义`v`变量，以上代码运行正常。

```javascript
console.log(typeof v); // 'undefined'

//let v;
```

块级变量是`typeof`**唯一**可能报错的场景，其他场景`typeof`总是可以正常运行。
