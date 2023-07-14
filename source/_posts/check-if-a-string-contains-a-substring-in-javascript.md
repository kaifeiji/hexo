---
title: JavaScript检查字符串是否包含子字符串
date: 2023-01-09 10:49:12
categories:
- Mastering JS
hide: true
tags:
- String
- Substring
---

原文：[Check if a String Contains a Substring in JavaScript](https://masteringjs.io/tutorials/fundamentals/contains-substring)

学习使用原生JavaScript检查字符串是否包含子字符串。

<!-- more -->

JavaScript有两种常用的方法用于检查字符串是否包含子字符串。更现代的方法是[String#includes()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/includes)函数。

```javascript
const str = 'Arya Stark';

str.includes('Stark'); // true
str.includes('Snow'); // false
```

[除了IE外的所有现代浏览器都可以使用String#includes()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/includes#Browser_compatibility)。在Node.js`>= 4.0.0`版本中也可以使用。

![](/post-images/check-if-a-string-contains-a-substring-in-javascript-2023-01-09-11-03-49.png)
*Mozilla Developer Network提供的兼容性表格⬆*

如果需要支持IE，应该用[String#indexOf()](https://www.w3schools.com/jsref/jsref_indexof.asp)方法代替，从1997年的ES1开始，它就是JavaScript的一部分了。

```javascript
const str = 'Arya Stark';

str.indexOf('Stark') !== -1; // true
str.indexOf('Snow') !== -1; // false
```

一般来说，如果不确定代码执行环境是否支持`includes()`，那么应该用`indexOf()`。`includes()`函数的语法只是比`indexOf()`略微简单一些。

## 大小写敏感

`String#includes()`和`String#indexOf()`都是大小写敏感的，而且都不支持正则表达式。如果需要进行大小写不敏感的检索，应该使用[String#match()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/match)函数和正则表达式，或者使用[String#toLowerCase()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/toLowerCase)函数将字符串和子字符串都转换为小写。

```javascript
const str = 'arya stark';

// 忽略大小写，检查子字符串最简洁的方法是`String#match()`和大小写不敏感的正则表达式(i)
str.match(/Stark/i); // true
str.match(/Snow/i); // false

// 也可以将检索的字符串和检索字符串都转换为小写。
str.toLowerCase().includes('Stark'.toLowerCase()); // true
str.toLowerCase().indexOf('Stark'.toLowerCase()) !== -1; // true

str.toLowerCase().includes('Snow'.toLowerCase()); // false
str.toLowerCase().indexOf('Snow'.toLowerCase()) !== -1; // false
```