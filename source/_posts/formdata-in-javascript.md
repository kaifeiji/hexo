---
title: JavaScript的FormData
date: 2023-07-25 16:48:54
categories:
- Mastering JS
hide: true
tags:
- FormData
---

原文：[FormData in JavaScript](https://masteringjs.io/tutorials/fundamentals/formdata)

`FormData`类在JavaScript上传文件时非常有用。

<!-- more -->

JavaScript的[FormData类](https://developer.mozilla.org/en-US/docs/Web/API/FormData)在[上传文件](https://masteringjs.io/tutorials/axios/form-data)时很常见。例如，给定一个文件input：

```html
<input type="file" id="my-input">
```

你可以创建一个`FormData`对象，传递到[axios的`post()`函数](https://masteringjs.io/tutorials/axios/post)，实现选择文件的上传。

```javascript
const input = document.querySelector('#my-input');

const formData = new FormData();
formData.append('myFile', input.files[0]);

axios.post('/upload', formData, {
  headers: {
    'Content-Type': 'multipart/form-data'
  }
});
```

简而言之，`FormData`类是最简单的上传文件的方式，不需要提交一个真正的HTML表单。

*本教程对您有帮助吗？来[GitHub仓库](https://github.com/mastering-js/masteringjs.io)点个星支持我们吧！*