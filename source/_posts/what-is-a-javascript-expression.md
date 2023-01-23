---
title: JavaScript的表达式
date: 2023-01-23 16:23:37
categories:
- Mastering JS
tags:
- 表达式
---

原文：[What is a JavaScript Expression?](https://masteringjs.io/tutorials/fundamentals/expressions)

类似Vue的许多框架，允许在HTML中内嵌JavaScript表达式。什么是表达式？可以在表达式加入`if`语句吗？

<!-- more -->

JavaScript的语法中[表达式和声明语句](https://2ality.com/2012/09/expressions-vs-statements.html)是有区别的。这两个概念有些微妙的不同之处，如果要理解[Vue](https://masteringjs.io/vue)一类的框架，要先理解二者的区别。

一般而言，表达式是可以计算为特定值的代码片段，语句是进行操作的代码片段。在JavaScript中，语句可以写成表达式。但反过来不成立：如果一个框架或JavaScript运行时需要一个表达式，则不能写为语句。

以下代码片段均为表达式，它们都可以计算为某个值。

```javascript
0 // 0

1 + 1 // 2

'Hello' + ' ' + 'World' // 'Hello World'

{ answer: 42 } // { answer: 42 }

Object.assign({}, { answer: 42 }) // { answer: 42 }

answer !== 42 ? 42 : answer // 42

answer = 42 // 42
```

以下代码片段均为语句，它们不能计算为特定的值。

```javascript
// `if` 语句
if (answer !== 42) { answer = 42 }
// `for` 循环是语句
for (;;) { console.log('Hello, World'); }
// 声明变量是语句
let answer = 42
```

## 函数参数

调用一个JavaScript函数时，所有的参数必须是表达式，不能是语句。

```javascript
function print(v) {
  console.log(v);
}

print('Hello, World'); // OK
print(true ? 'Hello, World' : null); // OK

print(if (true) { 'Hello, World' }); // SyntaxError: Unexpected token if
```

区分一个代码片段是表达式还是语句，一个简单的方法是，看它能否作为参数传递给`console.log()`。

以下代码显示，为变量赋值是表达式，而声明变量是语句。换言之，可以把变量赋值作为参数传递给函数，只要之前已经声明过这个变量。

```javascript
let answer;
console.log(answer = 42); // 42
answer; // 42

// SyntaxError
console.log(let value = 1);
```

## 立即调用函数表达式（IIFE）

JavaScript中，函数定义是表达式。

```javascript
console.log(function() {}); // '[Function]'
```

另外要注意的是[函数调用表达式](https://flaviocopes.com/javascript-expressions/#invocation-expressions)，调用函数也是表达式：

```javascript
const getAnswer = function getAnswer() { return 42 };

console.log(getAnswer()); // '42'
```

然而，在尝试声明函数的同时调用函数，发生了奇怪的现象。

```javascript
// SyntaxError
function getAnswer() { return 42 }();
```

这是因为特定的代码片段在不同的上下文，可能是表达式，也可能是语句。例如，`function getAnswer() { return 42; }`在赋值代码的右侧就是表达式，在一个文件的开头就是语句。

解决办法是把`function getAnswer() { return 42 }`放在圆括号中。圆括号，也就是[分组运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Grouping)，JavaScript会尝试将其中的代码作为表达式进行处理。

```javascript
// 42
(function getAnswer() { return 42 })();
```

这种用圆括号括起来立即执行的模式，叫做“立即调用函数表达式”（IIFE）。

## Vue

Vue一类的前端框架，可以将[JavaScript表达式绑定到HTML](https://vuejs.org/guide/essentials/template-syntax.html#Using-JavaScript-Expressions)。Vue绑定的必须是表达式，且只有一个表达式。

```javascript
const app = new Vue({
  data: () => ({ answer: null }),
  template: `
    <div>
      {{answer = 42}}
    </div>
  `
});
```

如果使用语句而非表达式，Vue会抛出一个模板编译异常。

```javascript
const app = new Vue({
  data: () => ({}),
  // 抛出异常 "Error compiling template"
  template: `
    <div>
      {{let answer = 42}}
    </div>
  `
});
```