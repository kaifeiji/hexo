---
title: JavaScript的立即调用函数表达式（IIFE）
date: 2023-07-25 17:08:24
categories:
- Mastering JS
tags:
- IIFE
---

原文：[Immediately Invoked Function Expressions (IIFE) in JavaScript](https://masteringjs.io/tutorials/fundamentals/iife)

JavaScript的立即调用函数表达式（IIFE）是一种常用于变量闭包的设计模式。

<!-- more -->

[立即调用函数表达式](https://developer.mozilla.org/en-US/docs/Glossary/IIFE)（简称IIFE）是一种JavaScript的设计模式，它声明了一个匿名函数并立即执行。

```javascript
// 打印 "Hello, World!"
(function() {
  console.log('Hello, World!');
})();
```

在IIFE模式中也可以用箭头函数：

```javascript
// 打印 "Hello, World!"
(() => {
  console.log('Hello, World!');
})();
```

`function() { ... }`外边的小括号是必要的：如果没有小括号，将会报语法错误。因为小括号告诉JavaScript语言解析器，将函数定义当作[表达式](https://masteringjs.io/tutorials/fundamentals/expressions)进行处理。

为何使用IIFEs？

IIFE非常有用，它可以定义IIFE之外无法访问的局部变量。例如，IIFE可以在浏览器顶层上下文中执行JavaScript，却不会污染全局作用域。

```html
<script>
  // 立即执行代码，`answer`不会成为全局变量
  (function() {
    var answer = 42;
  })();

  typeof answer; // 'undefined'
</script>
```

有时也会看到IIFE用于包裹一些计算需要用的临时变量，而这些临时变量又不想暴露给其他计算过程。例如，要计算购物车的总价，但又不想把`salesTax`变量泄露给外部作用域：

```javascript
const subtotal = 40;
const total = (function() {
  const salesTax = product.salesTaxRate * subtotal;
  return subtotal + salesTax;
})();
```

一元运算符

如果对IIFE使用一元运算符（例如[`void`运算符](https://masteringjs.io/tutorials/fundamentals/void#immediately-invoked-function-expressions-iifes)），可以省略IIFE外的小括号。

```javascript
// 打印 'Hello, World'
void function() { console.log('Hello, World'); }();
```

在生产环境很少见到这种模式用`void`。但这种模式在[async函数](https://masteringjs.io/tutorials/fundamentals/async-await)非常有用，因为`await`就是一个一元运算符。可以`await`一个async的IIFE：

```javascript
const answer = await async function() {
  return 42;
}();
answer; // 42
```

*本教程对您有帮助吗？来[GitHub仓库](https://github.com/mastering-js/masteringjs.io)点个星支持我们吧！*