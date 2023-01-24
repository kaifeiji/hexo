---
title: JavaScript字符串首字母大写
date: 2023-01-24 11:32:36
categories:
- Mastering JS
tags:
- 首字母大写
---

原文：[Capitalize the First Letter of a String in JavaScript](https://masteringjs.io/tutorials/fundamentals/capitalize)

JavaScript中如何将“dog”转为“Dog”。

<!-- more -->

JavaScript没有内置的字符串首字母大写函数，但原生JS可以用一行代码实现字符串的首字母大写。另外还可以使用[lodash](https://www.npmjs.com/package/lodash.capitalize)或CSS。本文介绍了这三种方法。

## 原生JS

JavaScript的[`String#uppercase()`函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/toUpperCase)可以将整个字符串转为大写。那么可以先把第一个字符大写，然后和[其他字符](https://masteringjs.io/tutorials/fundamentals/substring)拼起来，以实现首字母大写。

```javascript
function capitalize(str) {
  return str.charAt(0).toUpperCase() + str.slice(1);
}

capitalize('dog'); // 'Dog'
capitalize('cAT'); // 'CAT'
```

## lodash

[lodash的`capitalize()`函数](https://lodash.com/docs#capitalize)可以实现字符串的首字母大写，而且它还会将其他字符转换为小写。

```javascript
const _ = require('lodash');

_.capitalize('dog'); // 'Dog'
_.capitalize('cAT'); // 'Cat'
```

## CSS

在一些场景中，可能并不需要使用JavaScript。[CSS的`text-transform: capitalize`](https://css-tricks.com/almanac/properties/t/text-transform/#article-header-id-0)会将字符串中的每一个单词的首字母大写。

```javascript
.capitalize {
  text-transform: capitalize;
}
```

那么字符串`'capitalize using CSS'`在浏览器中会渲染为`'Capitalize Using CSS'`
