---
title: 原生JavaScript获取URL参数
date: 2023-07-27 16:47:58
categories:
- Mastering JS
hide: true
tags:
- URL
---

原文：[Get Query String Values in Vanilla JavaScript](https://masteringjs.io/tutorials/fundamentals/query-string)

本文展示了如何用原生JavaScript解析URL参数。

<!-- more -->

[`window.location.search`属性](https://www.w3schools.com/jsref/prop_loc_search.asp)包含原始的URL参数，例如，打开`http://localhost:5000/?answer=42`时，`window.location.search`等于`'?answer=42'`。

可以用URLSearchParams类对URL参数进行解析：

```javascript
const querystring = '?answer=42';
const params = new URLSearchParams(querystring);

params.get('answer'); // '42'
```

`URLSearchParams`的实例类似于Map数据类型，用[`keys()`函数](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams/keys)或[`entries()`函数](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams/entries)可以获取其中所有的key。注意：这两个函数返回的是遍历器**而非**数组，需要用`Array.from()`转换为数组。

```javascript
const querystring = '?answer=42&question=unknown';
const params = new URLSearchParams(querystring);

Array.from(params.keys()); // ['answer', 'question']
Array.from(params.entries()); // [['answer', '42'], ['question', 'unknown']]
```

## 其他方法

`URLSearchParams`在浏览器中支持良好，而且Node.js中也可用。但问题在于[IE并不支持`URLSearchParams`](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams#Browser_compatibility)。

`URLSearchParams`帮我们处理了许多边缘案例，但在大多场景下，可以用以下简单的函数解析URL参数。

```javascript
function parse(qs) {
  return qs.
    replace(/^\?/, '').
    split('&').
    map(str => str.split('=').map(v => decodeURIComponent(v)));
}

parse('?answer=42&question=unknown'); // [['answer', '42'], ['question', 'unknown']]
```

*本教程对您有帮助吗？来[GitHub仓库](https://github.com/mastering-js/masteringjs.io)点个星支持我们吧！*
