---
title: JavaScript比较两个字符串
date: 2023-01-09 23:16:15
categories:
- Mastering JS
tags:
- String
- 对比
---

原文：[Compare Two Strings in JavaScript](https://masteringjs.io/tutorials/fundamentals/string-compare)

学习如何比较两个字符串：哪个更大、更小或相等。

<!-- more -->

JavaScript比较字符串很简单，要判断两个字符串是否相等，使用`===`。[切记不要用==](https://codeburst.io/javascript-double-equals-vs-triple-equals-61d4ce5a121a)。

```javascript
const str1 = '1st string';
const str2 = str1;
const str3 = '2nd string';

str1 === str2; // true
str1 === str3; // false

// 要使用 `===`, 因为 `==` 会有意外情况发生
'1' == 1; // true
'2' == 2; // true
```

## < 和 >

`>`和`<`，根据[Unicode排序](https://www.w3.org/TR/xml-entity-names/bycodes.html)按字典顺序比较字符串：数字总是`<`大写字母，大写字母总是`<`小写字母。

```javascript
const str1 = '0';
const str2 = 'A';
const str3 = 'Z';
const str4 = 'a';

str1 < str2; // true
str2 < str3; // true
str3 < str4; // true
```

比较长度大于1的字符串时，JavaScript逐字进行比较。如果两个字符串以相同字符开头，JavaScript会比较二者的第二个字符。字符串的截止总是`<`任何字符（译注：作者想表达的意思是`'A' < 'A1'`）。

```javascript
// 空字符串 '' < 所有其他字符串
const str1 = '';
const str2 = 'A';
const str3 = 'A1';
const str4 = 'Z0';

str1 < str2; // true
str2 < str3; // true
str3 < str4; // true
```

`<`和`>`运算符，比较字符串和其他类型时，会返回`false`：

```javascript
1 < 'A'; // false
'A' < 1; // false

null < 'A'; // false
'A' < null; // false

undefined < 'A'; // false
'A' < undefined; // false
```

## 排序

[Array#sort()函数默认将数组的所有值转换为字符串，然后按Unicode顺序进行排序](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort#Description)。`sort()`函数会将`null`和`undefined`值放置到数组的末尾。

```javascript
1 < 'A'; // false
'A' < 1; // false

null < 'A'; // false
'A' < null; // false

undefined < 'A'; // false
'A' < undefined; // false
```

## <=和>=

使用`<=`和`>=`时要注意：它们和`==`类似，会进行同类型强制转换。

```javascript
'1' <= 1; // true
```