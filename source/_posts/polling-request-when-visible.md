---
title: 减少轮询消耗——可见轮询(IntersectionObserver和visibilitychange)
date: 2021-06-29 07:58:35
categories:
- 前端
tags:
- IntersectionObserver
- visibilitychange
- 轮询
- polling
- JavaScript
---

虽然Websocket已经开始普及，但轮询仍有其应用场景。

本文采用IntersectionObserver API和visibilitychange事件，实现在页面或元素可见时，才触发轮询，不可见时自动停止，减少前后台消耗。

<!-- more -->

## 可见轮询方法

``` js
/**
 * @param callback function - 轮询请求函数
 * @param interval number - 轮询间隔，毫秒
 * @param el HTMLElement - 要监视的元素，可为空，默认为document.html
 * @return number - setInterval编号
 */
function polling(callback, interval=5000, el=document.html) {
  let seen = false;
  let hidden = false;
  const io = new IntersectionObserver(ioEntry => {
    if (ioEntry[0].intersectionRatio > 0) {
      seen = true;
    } else {
      seen = false;
    }
  });
  io.observe(el || document.html);
  document.addEventListener('visibilitychange', e => {
    if (document.hidden) {
      hidden = true;
    } else {
      hidden = false;
    }
  });
  setInterval(() => {
    if (seen && !hidden) {
      callback?.();
    }
  }, interval);
}
```

## 调用

``` js
// 监视id为list的元素，可见时执行轮询（5秒一次）
const getDataPolling = polling(() => {
  // 获取数据、处理数据逻辑
  getData();
  processData();
},5000, document.querySelector('#list'));
```
