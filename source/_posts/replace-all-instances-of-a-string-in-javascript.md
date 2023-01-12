---
title: JavaScript替换字符串中所有给定子字符串
date: 2023-01-12 22:35:06
categories:
- Mastering JS
tags:
- replace
---

原文：[Replace All Instances of a String in JavaScript](https://masteringjs.io/tutorials/fundamentals/string-replace)

学习如何替换JavaScript字符串中所有给定子字符串。

<!-- more -->

[String#replace()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/replace)默认只替换第一个子字符串。要替换所有，需要[传入一个带有`/g`标识的正则表达式](http://2ality.com/2013/08/regexp-g.html)，如下：

```javascript
const str = 'A penny saved is a penny earned';

// "A dollar saved is a dollar earned"
str.replace(/penny/g, 'dollar');

// "A dollar saved is a penny earned" - 默认只替换第一个.
str.replace('penny', 'dollar');
```

注意，[JavaScript的字符串是不可变的](https://www.sitepoint.com/immutability-javascript/)，字符串使用`replace()`，并不会修改原始的字符串。

## 替换变量名

[`String#replace()`函数的第二个参数，有几个特殊的字符组合，称为“替换变量名”](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/replace#Specifying_a_string_as_a_parameter)。替换变量名可以用于这样的场景：把所有正则表达式匹配的子字符串，替换为包含匹配内容的字符串。

例如，在所有数字前加一个`#`。使用`$&`替换变量名，就可以在匹配子字符串前插入#了。

```javascript
const str = 'My favorite team is 1, everyone else is 2';

// "My favorite team is #1, everyone else is #2"
str.replace(/\d+/g, '#$&');
```

## 译者补充

替换所有子字符串可以使用ES6新增的[String#replaceAll()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/replaceAll)。