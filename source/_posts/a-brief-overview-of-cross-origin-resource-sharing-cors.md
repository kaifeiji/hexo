---
title: CORS（跨域资源共享）的简单总结
date: 2023-02-03 16:48:23
categories:
- Mastering JS
hide: true
tags:
- CORS
---

原文：[A Brief Overview of Cross-Origin Resource Sharing (CORS)](https://masteringjs.io/tutorials/fundamentals/cors)

跨域资源共享，简写为CORS，是浏览器端的一项协议，用于确定从其他域请求一个资源是否安全。本文简要介绍。

<!-- more -->

[CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)一种浏览器端确定在不同域进行HTTP请求是否安全的协议。浏览器会限制JavaScript的跨域请求，如果用`fetch()`或[axios](https://masteringjs.io/axios)向[没有设置CORS的express服务](https://masteringjs.io/tutorials/express/cors)发起请求，会得到以下错误信息：

```
Access to fetch at 'http://localhost:3000/' from origin 'http://localhost:5000' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.
```

## 什么是跨域？

可以认为域就是浏览器地址栏中的地址。例如，浏览器打开

`http://localhost:5000/tutorials/fundamentals/pojo`

![](/post-images/a-brief-overview-of-cross-origin-resource-sharing-cors-2023-02-03-17-25-43.png)

以下请求被认为是跨域的：

* `https://localhost:5000/test` - 协议不同, `http` `https`
* `http://localhost:3000/test` - 端口不同, `3000` `5000`
* `http://google.com:5000/test` - 域名不同, `localhost` `google.com`

换言之，与地址栏中的协议、域名和端口不匹配的请求都是跨域的。

## 设置CORS支持

在WEB服务器上需要设置CORS，例如在express中用[`cors`插件](https://masteringjs.io/tutorials/express/cors)。如果发起跨域请求的服务无法更改，唯一的办法就是设置一个代理（译注：在本域后台服务中设置一个代理）。

大部分浏览器会用HTTP的`OPTIONS`方法（而非`GET`或`POST`）发起一个[预检请求](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS#Functional_overview)，检查CORS的headers。为了支持CORS，后台服务需要接收`OPTIONS`请求，并在response中设置`Access-Control-Allow-Origin`的header。

一般是使用[npm包`cors`](https://www.npmjs.com/package/cors)，但以下示例中，只需要简单的设置response的header，就能支持跨域请求，这在任何WEB服务框架中都非常轻松实现。

```javascript
const app = require('express')();

// 需要处理 'OPTIONS' 预检请求
app.options('*', (req, res) => {
  res.set('Access-Control-Allow-Origin', '*');
  res.send('ok');
});

// 在实际的response中也要设置'Access-Control-Allow-Origin'
app.get('/', (req, res) => {
  res.set('Access-Control-Allow-Origin', '*');
  res.send('Hello, World!')
});
const server = await app.listen(3000);
```