---
title: JavaScript禁用按钮
date: 2023-07-25 16:57:44
categories:
- Mastering JS
tags:
- Button
- Disable
---

原文：[Disable a Button in JavaScript](https://masteringjs.io/tutorials/fundamentals/disable-button)

如何用原生JavaScript禁用一个按钮。

<!-- more -->

在JavaScript中，[button元素可以设置一个`disabled`属性](https://www.w3schools.com/jsref/prop_pushbutton_disabled.asp)，来阻止点击按钮。例如，一个点击弹出alert的按钮：

```html
<button id="my-alert-button">Show Alert</button>
<script>
  document.querySelector('#my-alert-button').addEventListener('click', function() {
    alert('Clicked on button!');
  });
</script>
```

以下是该按钮的实例。

> 静态博客无法显示，请查看原文

如果按钮设置了`disabled`属性，点击按钮不会发生任何事。

```html
<button id="my-alert-button">Disabled Button</button>
<script>
  document.querySelector('#my-alert-button').addEventListener('click', function() {
    alert('Clicked on button!');
  });
  // 现在点击按钮不会发生任何事
  document.querySelector('#my-alert-button').disabled = true;
</script>
```

> 静态博客无法显示，请查看原文

## 表单

按钮也可以用来提交[表单](https://www.w3schools.com/jsref/dom_obj_form.asp)。例如，点击以下按钮会触发`my-form`的submit事件。在输入框聚焦状态按回车也可以提交表单。

```html
<form id="my-form">
  <input type="text" placeholder="Sample Input">
  <button type="submit">Submit Form</button>
</form>
<script>
  document.querySelector('#my-form').addEventListener('submit', function(ev) {
    ev.preventDefault();
    alert('Form submitted!');
  });
</script>
```

> 静态博客无法显示，请查看原文

如果禁用了按钮，就相当于禁用了表单的提交。当点击按钮时不会触发`my-form`的submit事件，在input聚焦状态按回车也不会。

```html
<form id="my-form">
  <input type="text" placeholder="Sample Input">
  <button type="submit">Submit Form</button>
</form>
<script>
  document.querySelector('#my-form').addEventListener('submit', function(ev) {
    ev.preventDefault();
    alert('Form submitted!');
  });
  document.querySelector('#my-form button').disabled = true;
</script>
```

> 静态博客无法显示，请查看原文

*本教程对您有帮助吗？来[GitHub仓库](https://github.com/mastering-js/masteringjs.io)点个星支持我们吧！*