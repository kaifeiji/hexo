---
title: 用FileReader读取本地文件
date: 2023-07-25 15:44:22
categories:
- Mastering JS
tags:
- File
---

原文：[Read Local Files in JavaScript with FileReader](https://masteringjs.io/tutorials/fundamentals/filereader)

FileReader类可以从原生的文件input中读取文件。

<!-- more -->

[JavaScript的`FileReader`类](https://developer.mozilla.org/en-US/docs/Web/API/FileReader)可以在浏览器中读取用户机器上的文件。FileReader一般通过`<input type="file">`来读取数据。

例如，在页面上有一个id为`select-file`的文件input，可以用以下代码打印选中文件的内容。

```javascript
const file = document.querySelector('#select-file').files[0];
const reader = new FileReader();

reader.onload = res => {
  console.log(res.target.result); // Print file contents
};
reader.onerror = err => console.log(err);

reader.readAsText(file);
```

以下是一个实例，可以在每次选择不同文件时，在控制台输出文件的内容。在Linux/Windows中按Ctrl+Shift+J，或在OSX中按Cmd+J，打开Chrome的控制台，然后试试吧！

> 静态博客无法显示，请查看原文

`FileReader`在现代浏览器和IE10中支持良好。需要注意`FileReader`是浏览器的API，所以大部分浏览器支持，但`FileReader`并不是Node.js的一部分。

Promise和async/await
FileReader类的异步API在配合[async/await](https://masteringjs.io/tutorials/fundamentals/async-await)或[Promise链](https://masteringjs.io/tutorials/fundamentals/promise-chaining)使用时并不是很理想。以下代码将`FileReader`包裹在Promise中，可以用于链式操作：

```javascript
function readFile(file) {
  return new Promise((resolve, reject) => {
    const reader = new FileReader();

    reader.onload = res => {
      resolve(res.target.result);
    };
    reader.onerror = err => reject(err);

    reader.readAsText(file);
  });
}
```

有了以上的`readFile()`帮助函数，可以在异步函数中读取文件：

```javascript
async function onSubmit() {
  const file = document.querySelector('#select-file').files[0];

  const contents = await readFile(file);
}
```

*本教程对您有帮助吗？来[GitHub仓库](https://github.com/mastering-js/masteringjs.io)点个星支持我们吧！*