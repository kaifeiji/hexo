---
title: JavaScript中substring、substr和slice的区别
date: 2023-01-14 20:27:57
categories:
- Mastering JS
tags:
- substring
- substr
- slice
---

原文：[Substring vs Substr vs Slice in JavaScript](https://masteringjs.io/tutorials/fundamentals/substring)

JavaScript有3种方法可以获取字符串的子串。本文介绍了`String#substring()`、`String#substr()`和`String#slice()`的区别。

<!-- more -->

[String#substring()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/substring)和[String#substr()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/substr)函数的区别带有普遍的迷惑性，甚至很多有经验的JavaScript开发者有时也会弄混。另外还有第三种方法来获取子串——[String#slice()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/slice)函数，也是很常见的。本文介绍了JavaScript获取子串的这三种方法的区别。

## String#substring()

`substring()`函数是JavaScript最常用的获取子串的方法。它接收两个参数：`indexStart`和`indexEnd`。它返回从`indexStart`开始、到`indexEnd`之前结束的子串。例如：

```javascript
const str = 'Twas the night before Christmas';

let indexStart = 0;
let indexEnd = 4;
str.substring(indexStart, indexEnd); // 'Twas'

str.substring(5, 14); // 'the night'
```

如果不指定`indexEnd`，`substring()`函数会返回从`indexStart`开始直到结尾的子串。

```javascript
str.substring(5); // 'the night before Christmas'
```

`substring()`函数在边界情况有一些奇怪的行为：

* 如果`indexStart`或`indexEnd`小于0，会按0处理。
* 如果`indexEnd < indexStart`，二者会互换。

例如，`substring(4, -1)`和`substring(4, 0)`是等效的，和`substring(0, 4)`也是等效的，如下：

```javascript
str.substring(4, -1); // 'Twas'
```

## String#substr()

`substr()`函数也很常用，[但它在Mozilla的文档中被认为是“传统函数”](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/substr)。当你写新代码时不应该使用它，但你可能在已有的JavaScript项目中发现该函数。

`substring()`和`substr()`的主要不同之处，是`substr()`的第二个参数。`substr()`的第一个参数是`start`，第二个参数是`length`。例如：

```javascript
const str = 'Twas the night before Christmas';

let start = 0;
let length = 4;
// 如果 `start === 0`, `substr()` 和 `substring()` 是等效的
str.substr(start, length); // 'Twas'

str.substr(5, 9); // 'the night'
'the night'.length; // 9
```

和`substring()`不同，`substr()`可以使用负的`start`，`substr()`会反过来从结尾开始数`start`。例如：

```javascript
const str = 'Twas the night before Christmas';

let start = -9;
let length = 9;
str.substr(start, length); // 'Christmas'

'Christmas'.length; // 9
```

## String#slice()

`slice()`函数对比`substring()`和`substr()`，相对少见一些。但是它具备`substring()`和`substr()`二者的优点。`slice()`函数和`substring()`类似，接收开始和结束的index作为参数，但它并不是传统函数。`slice()`函数和`substr()`类似，也支持负值index。例如：

```javascript
const str = 'Twas the night before Christmas';

str.slice(0, 4); // Twas
str.slice(5, 14); // the night
str.slice(-16, -10); // before
str.slice(-9); // Christmas
```

`slice()`函数很明显从三者中胜出：

* 不是一个“传统函数”
* 支持负值的index
* 不容易混淆：没有`String#splice()`