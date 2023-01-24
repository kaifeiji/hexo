---
title: JavaScript中void 0的作用
date: 2023-01-23 16:23:46
categories:
- Mastering JS
tags:
- void
---

原文：[What Does void 0 Do in JavaScript?](https://masteringjs.io/tutorials/fundamentals/void)

JavaScript中的void运算符是一个普遍的难点，它很少出现在教程中，但它有一些方便的用途。本文展示了void运算符最常见的一些用途。

<!-- more -->

[JavaScript的`void`运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/void)对[表达式](https://masteringjs.io/tutorials/fundamentals/expressions)进行计算然后返回`undefined`。乍一看，这个运算符没什么用，但在实践中有3种情况会用到`void`运算符。以下是3个主要的用途：

## 禁止覆盖undefined

一个令人吃惊的事实：[`undefined`是一个合法的JavaScript变量名](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/undefined#Description)。以下代码会打印`test`两次。

```javascript
const undefined = 'test';

function foo() {
  return undefined;
}

function bar(undefined) {
  return undefined;
}

console.log(foo());
console.log(bar('test'));
```

而`void`是一个运算符，如果尝试以`void`命名变量，JavaScript会抛出以下异常。

```javascript
SyntaxError: Unexpected token void
```

一些JavaScript项目使用`void 0`来替代`undefined`，避免意外创建以`undefined`命名的变量。甚至ESLint为了这个原因，专门设定了一个规则，[禁止使用`undefined`变量](https://eslint.org/docs/latest/rules/no-undefined)。

## 无操作`<a>`标签

`void`运算符另一个用途，是`<a>`标签的URI设为`javascript:void(0)`，点击时不做任何事。`<a>`标签如果没有`name`或`href`属性，是无效的HTML元素。使用`javascript:void(0)`是常见的技巧，可以不写入真实的链接，使`<a>`标签也能正常渲染。

```html
<a href="javascript:void(0)">Click Here</a>
```

以上HTML效果如下：

[Click Here](javascript:void(0))

## 立即调用函数表达式（IIFE）

声明一个函数并在同一个语句执行函数，一般需要用圆括号括起来，[强制JavaScript将函数声明作为表达式进行处理](https://masteringjs.io/tutorials/fundamentals/expressions#immediately-invoked-function-expressions-iifes)。

```javascript
(function() { console.log('Hello, World'); })(); // 打印 "Hello, World"
```

有时候`void`运算符可以替代圆括号。`void`是一元运算符，JavaScript将它之后的函数声明作为表达式处理，因此以下代码打印“Hello, World”。

```javascript
void function() { console.log('Hello, World'); }();
```

二者的区别是，圆括号的IIFE仍然会返回一个值，而void的IIFE始终返回undefined。

```javascript
(function() { return 42; })(); // 42

void function() { return 42; }(); // undefined
```