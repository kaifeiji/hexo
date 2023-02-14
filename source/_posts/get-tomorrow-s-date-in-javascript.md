---
title: JavaScript获取明天的日期
date: 2023-02-11 16:39:27
categories:
- Mastering JS
tags:
- Date
---

原文：[Get Tomorrow's Date in JavaScript](https://masteringjs.io/tutorials/fundamentals/tomorrow)

虽然用Moment.js一类的库很容易操作日期，但原生JavaScript也能获取明天日期。

<!-- more -->

[JavaScript的内置`Date`类](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)有获取和设置日期中的日的getter和setter。[`Date#getDate()`函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/getDate)可以返回日期中的日：

```javascript
// 本地时区2019年6月3日
const date = new Date('2019/06/03');

date.getDate(); // 3
```

`Date#setDate()`函数可以设置日期中的日。

```javascript
// 本地时区2019年6月3日
const date = new Date('2019/06/03');

date.setDate(6);
date.getDate(); // 6

// "2019年6月06日周四"
date.toLocaleString('zh-CN', {
  weekday: 'short',
  month: 'long',
  day: '2-digit',
  year: 'numeric'
});
```

见[《用原生JavaScript格式化日期》](https://masteringjs.io/tutorials/fundamentals/date_format)。

要获取明天的日期，在当前日的基础上加一，然后`setDate()`即可。

```javascript
// 当前日期
const date = new Date();

// 明天日期
date.setDate(date.getDate() + 1);
```

JavaScript会自动处理月份的更迭，今天是6月30日，`date.getDate() + 1`的方式也奏效：

```javascript
const date = new Date('2019/06/30');

// 第二天
date.setDate(date.getDate() + 1);

// "2019年7月01日周一"
date.toLocaleString('zh-CN', {
  weekday: 'short',
  month: 'long',
  day: '2-digit',
  year: 'numeric'
});
```

## 用[Moment.js](https://momentjs.com/)

Moment只需要简单的调用`.add()`函数，即可在当前日期基础上加一天。

```javascript
const date = moment(new Date('2019/06/30'));

date.add(1, 'days');

date.format('YYYY/MM/DD'); // "2019/07/01"
```