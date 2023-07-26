---
title: JavaScript的Blob
date: 2023-07-25 16:37:41
categories:
- Mastering JS
hide: true
tags:
- Blob
---

原文：[Blobs in JavaScript](https://masteringjs.io/tutorials/fundamentals/blob)

Blob（二进制大型对象）是JavaScript中一种类似于文件的数据结构，相当于客户端的Buffer。

<!-- more -->

[Blob](https://developer.mozilla.org/en-US/docs/Web/API/Blob)是一个包含任意字节的对象。`Blob`类是浏览器[File API](https://www.w3.org/TR/FileAPI/)的一部分：JavaScript的`File`类是`Blob`的子类，所以当你从`<input type="file">`中获取一个文件实例时，它其实也是`Blob`的实例。

```javascript
const input = document.querySelector('input[type="file"]');
const file = input.files[0];

file instanceof File; // true
file instanceof Blob; // true
```

和[FileReader](https://masteringjs.io/tutorials/fundamentals/filereader)一样，`Blob`类在各种浏览器中支持良好，但Node.js**不**支持。[Node.js的Buffer](https://masteringjs.io/tutorials/node/buffer)和Blob很相似，他们都存储任意字节，但它们时完全不同的API。

Blob有一个简洁的[`createObjectURL()`函数](https://developer.mozilla.org/en-US/docs/Web/API/URL/createObjectURL)，经常用在预览上传图片预览。给定一个Blob，`URL.createObjectURL()`创建一个指向该Blob的URL，可以直接用在`src`或`href`属性中。

例如，如果你点击以下文件input，选择一张图片，你会看到选择图片的预览效果。

> 静态博客无法显示，请查看原文

以下是这个input的JavaScript代码，使用`URL.createObjectURL()`来创建一个指向上传文件的本地URL，不需要将文件真正上传到服务器。

```javascript
const input = document.querySelector('#data-url-example');

input.addEventListener('change', () => {
  const file = input.files[0];
  const url = URL.createObjectURL(file);

  document.querySelector('#data-url-container').innerHTML = `
    <img src="${url}" />
  `;
});
```

*本教程对您有帮助吗？来[GitHub仓库](https://github.com/mastering-js/masteringjs.io)点个星支持我们吧！*