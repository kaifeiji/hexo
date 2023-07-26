---
title: 字符串首字母大写
date: 2023-07-26 17:11:02
categories:
- Mastering JS
hide: true
tags:
- String
---

原文：[Capitalize the First Letter of a String in JavaScript](https://masteringjs.io/tutorials/fundamentals/capitalize-first-letter)

不用任何外部库，实现字符串首字母大写。

<!-- more -->

用字符串的[`toUpperCase()`方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/toUpperCase)和[`slice()`方法](https://masteringjs.io/tutorials/fundamentals/substring)可以轻松实现字符串的首字母大写。

```javascript
const str = 'captain Picard';

const caps = str.charAt(0).toUpperCase() + str.slice(1);
caps; // 'Captain Picard'
```

首先将第一个字母转为大写，然后拼接剩余的字符。

如果要将字符串中的每一个单词首字母大写，可以用`split()`将字符串分割成单词，分别首字母大写后再用`join()`拼接成字符串，如下。

```javascript
const str = 'captain picard';

function capitalize(str) {
  return str.charAt(0).toUpperCase() + str.slice(1);
}

const caps = str.split(' ').map(capitalize).join(' ');
caps; // 'Captain Picard'
```

## 使用CSS

在前端，字符串的首字母大写并不需要JavaScript，CSS完全可以实现：

```css
.capitalize {
  text-transform: capitalize;
}
```

例如，以下`<div>`包含`capitalize`样式类，内部文本是captain picard，CSS可以将字符串中所有单词转为首字母大写。

```html
<div class="capitalize">captain picard</div>
```

*本教程对您有帮助吗？来[GitHub仓库](https://github.com/mastering-js/masteringjs.io)点个星支持我们吧！*