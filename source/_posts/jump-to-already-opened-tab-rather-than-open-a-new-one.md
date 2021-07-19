---
title: 跳转到已打开的tab页，而非重新打开一个
date: 2021-07-09 10:04:12
categories:
- 前端
tags:
- window.open
- Javascript
- 页面跳转
---

一个`<a href target="_blank">`标签，总是会打开新tab页。

使用window.open，可以实现跳转到已打开的tab页，而非重新打开一个。

<!-- more -->

## 直接上代码

``` html HTML
<span onclick="jump()">跳转到163邮箱</span>
```

``` js JS
function jump() {
  var newTab = window.open('', '163_mail');
  if (newTab.location.href !== 'https://mail.163.com') {
    window.open('https://mail.163.com',  '163_mail');
  }
}
```

## 代码讲解

``` js
var newTab = window.open('', '163_mail');
```

其中第一个参数`strUrl`是要打开的链接地址，第二个参数`strWindowName`是打开新tab页的名称。

如果`strWindowName`不为空，浏览器会尝试在已打开的标签页中，寻找以此为名称的tab页。

如果找到了，则直接跳转到该tab页；如果没找到，则打开一个新的`about:blank`tab页（因为`strUrl`为空）。

``` js
if (newTab.location.href !== 'https://mail.163.com') {
  window.open('https://mail.163.com',  '163_mail');
}
```

判断跳转或打开的tab页，地址是否为目标URL。

如果匹配，什么也不做；如果不匹配，则在该tab页打开目标URL。

## 参考

[window.open-MDN文档](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/open)
