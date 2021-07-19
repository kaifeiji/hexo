---
title: 页面间交互——window.postMessage和Broadcast Channel API
date: 2021-07-08 12:58:07
categories:
- 前端
tags:
- Javascript
- 通信
---

在多页面Web应用中，会遇到页面间交互的场景，例如：导航页向iFrame页传递数据，tab页间通知事件等。

本文介绍两种常见的页面间交互的接口——window.postMessage和Broadcast Channel API，对比二者的区别。

<!-- more -->

## 消息传递方向

### window.postMessage

window.postMessage的调用示例如下：

``` js A页面
const B = window.open(B.html);
B.postMessage("hello B!", "*");
```

``` js B页面
function receiveMessage(ev) {
  alert(ev.data);
}
window.addEventListener("message", receiveMessage, false);
```

从代码可以看出，A页面向B页面传递了一个`Hello`字符串，B页面通过监听收到并alert出来。

window.postMessage的传递方向是**单向**，且必须指定接收方的（实际上是调用了接收方的API）。

### Broadcast Channeel API

Broadcast Channeel API的调用示例如下：

``` js A页面
const bc = new BroadcastChannel('fm86.7');
bc.onmessage = function (ev) {
  alert(ev.data);
}
bc.postMessage('Music Radio');
```

``` js B页面
const bc = new BroadcastChannel('fm86.7');
bc.onmessage = function (ev) {
  alert(ev.data);
}
```

``` js C页面
const bc = new BroadcastChannel('fm86.7');
bc.onmessage = function (ev) {
  alert(ev.data);
}
```

从代码可以看出，三个页面都接入了`fm86.7`频道，A**广播**了`Music Radio`字符串，B和C都会接收到，且alert出来。

## 适用场景

### 单页/多页

大多单页应用都依赖内置或插件实现全局状态管理，不太需要使用本文的两种方法。

多页应用存在跨页面的交互时，可以考虑使用这两种API。

### 定向/广播/跨域

window.postMessage一般用于定向传递数据，而且可以实现跨域交互（可以设置targetOrigin保证安全）。

Broadcast Channel API是一种广播，接入对应频道的所有页面都能接收到，但仅局限于本域，不支持跨域。

## MDN文档

更具体的使用方法和案例，见MDN文档，不在此赘述。

[window.postMessage-MDN文档](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/postMessage)

[Broadcast Channel API-MDN文档](https://developer.mozilla.org/zh-CN/docs/Web/API/Broadcast_Channel_API)

## 其他方法

页面间交互，还有其他更多的方法：

1. 使用URL参数传值（?param=value），用URLSearchParams API接收，但只能在打开页面时，传递一次

2. 直接操作parent、top（Window对象），或iFrame的contentWindow（Window对象）

3. Channel Messaging API，只支持两个页面的双向通信

4. 通过localStorage传递数据，可以主动获取，也可以使用`window.addEventListener("storage", callback)`事件触发获取（同理，IndexedDB、Cookie等存储方案都可以实现页面间传数据）

5. Shared Worker或Service Worker

这些方法难度依次递增，当然功能也更强大，供参考！
