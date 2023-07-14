---
title: JavaScript拼接字符串3种方法
date: 2023-01-25 12:00:17
categories:
- Mastering JS
hide: true
tags:
- 字符串
---

原文：[3 Ways to Concatenate Strings in JavaScript](https://masteringjs.io/tutorials/fundamentals/string-concat)

JavaScript中可以用`+`运算符、`Array#join()`函数和`String#concat()`函数拼接字符串。本文介绍了一些需要了解的知识。

<!-- more -->

JavaScript有三种方法可以拼接字符串。本文讲解了这三种方法以及其中的差异。

## + 运算符

用于两个数值加和的运算符`+`，也能用于拼接两个字符串。

```javascript
const str = 'Hello' + ' ' + 'World';
str; // 'Hello World'
```

也可以用`+=`，`a += b`相当于`a = a + b`的简写。

```javascript
let str = 'Hello';
str += ' ';
str += 'World';
str; // 'Hello World'
```

如果运算符`+`左侧是字符串，JavaScript会将右侧的强制转换为字符串。也就是说字符串拼接对象、数值、`null`或`undefined`都是没问题的。

```javascript
let str = 'Values: ';
str += 42;
str += ' ';

str += {};
str += ' ';

str += null;

str; // 'Values: 42 [object Object] null'
```

运算符`+`和`+=`[在现代的JavaScript引擎中是很快的](https://2ality.com/2011/10/string-concatenation.html)，不必担心类似[Java的StringBuilder类](https://www.geeksforgeeks.org/stringbuilder-class-in-java-with-examples/)的问题。

## Array#join()

[`Array#join()`函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/join)将数组中所有元素拼接起来，生成一个新字符串，例如：

```javascript
['Hello', ' ', 'World'].join(''); // 'Hello World'
```

`join()`的第一个参数是分隔符，默认的分隔符是逗号`','`。

```javascript
['a', 'b', 'c'].join(); // 'a,b,c'
```

可以使用任何字符作为分隔符。分隔符的存在，让Array#join()在重复拼接相同字符时更方便，例如，用空格`' '`组为分隔符拼接单词数组：

```javascript
// 'Twas the night before Christmas'
['Twas', 'the', 'night', 'before', 'Christmas'].join(' ');
```

也可以使用`'/'`拼接URL片段：

```javascript
// 'masteringjs.io/tutorials/fundamentals/string-concat'
['masteringjs.io', 'tutorials', 'fundamentals', 'string-concat'].join('/');
```

`Array#join()`的操作符，使得这种拼接字符串的方法非常灵活。如果需要拼接的字符串数量不确定，应该使用`join()`，而不是`for`循环嵌套`+`。

## String#concat()

[JavaScript的字符串有内置的`concat()`方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/concat)。`concat()`函数接收一个或多个参数，返回修改的字符串。JavaScript的字符串是不可变的，所以`concat()`不会修改原字符串。

```javascript
const str1 = 'Hello';
const str2 = str1.concat(' ', 'World');

// 'Hello'. 字符串是不可变的, 所以 `concat()` 不会修改 `str1`
str1;
// 'Hello World'
str2;
```

`concat()`的缺点是`str1`必须是字符串。如果`str == null`会抛出`TypeError`，但可以向`concat()`传入非字符串参数。

```javascript
// 如果 `str` 是 null 或其他非字符串, 不能使用 `concat()`
const str = 'Values: ';

// 'Values: 42 null'
str.concat(42, ' ', null);
```

`concat()`函数很少使用，因为它比运算符`+`有更多的异常场景。例如，调用`concat()`的变量[恰巧是一个数组](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/concat)，可能会发生一些超出预期的行为。除非有充分的理由，否则应该用`+`替代`concat()`。

如果必须使用`concat()`，最好用空字符串调用：

```javascript
''.concat('Hello', ' ', 'World');
```