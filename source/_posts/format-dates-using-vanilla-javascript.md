---
title: 使用原生JavaScript格式化日期
date: 2023-01-05 18:55:20
categories:
- 前端
tags:
- 日期
- 格式化
---

原文：[Format Dates Using Vanilla JavaScript](https://masteringjs.io/tutorials/fundamentals/date_format)

学习使用`toLocaleString()`函数格式化日期，不用外部库。

<!-- more -->

JavaScript有许多像[moment](https://www.npmjs.com/package/moment)这样的库，可以格式化日期。然而对于基本的日期格式化而言，你并不需要它们：内置的[Date#toLocaleString()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/toLocaleString)函数可以不用外部库，转换为自定义的字符串格式。

## toLocaleString()介绍

`toLocaleString()`函数接收2个参数：

1. 一个字符串参数`locale`，代表[日期的地区格式](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl#locales_argument)，例如`'en-US'`或`'default'`。

1. 一个对象参数`options`，包含日期的不同部分如何格式化，例如`month`或`minutes`，见[`toLocaleDateString()`的options参数完整列表](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/DateTimeFormat/DateTimeFormat)。

以下是一个日期格式化的例子，包含短周和完整日期。

```javascript
// 结尾不带'Z'，JavaScript不会使用相对于UTC的时区。
const date = new Date('2019-06-01T00:00:00.000');

// "Sat, June 01, 2019"
date.toLocaleString('en-US', {
  weekday: 'short', // "Sat"
  month: 'long', // "June"
  day: '2-digit', // "01"
  year: 'numeric' // "2019"
});
```

注意：别把`toLocaleString()`和`toLocaleDateString()`搞混了。`toLocaleDateString()`函数和`toLocaleString()`很相似，但它默认不包含日期中的时间部分。

```javascript
const date = new Date('2019-06-01T08:00:00.000');

// "6/1/2019, 8:00:00 AM"
date.toLocaleString('en-US');
// "6/1/2019" 不带时间部分
date.toLocaleDateString();

// 但你也可以在 `toLocaleDateString()` 中使用 options 包含 `hours` 和 `minutes` 
date.toLocaleDateString('en-US', {
  month: 'long',
  day: '2-digit',
  year: 'numeric',
  hour: '2-digit'
}); // "June 01, 2019, 8 AM"
```

## 时区

 `toLocaleString()`函数可以在格式化日期时指定时区。支持的时区是[基于实现](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/toLocaleString#Parameters)的，所以必须先在具体的环境中进行测试，才能使用像`'America/Los_Angeles'`或`'America/New_York'`这样的时区名。例如，[Node.js从操作系统读取时区数据](https://github.com/nodejs/help/issues/1843)，这意味着Node进程很大可能是包含时区数据的，但并不能绝对保证。

以下是使用一个特定时区——`'America/Denver'`——格式化UTC日期的例子。

```javascript
const date = new Date('2019-06-01T08:00:00.000Z');
// "June 01, 2019, 2 AM"
date.toLocaleDateString('en-US', {
  month: 'long',
  day: '2-digit',
  year: 'numeric',
  hour: '2-digit',
  timeZone: 'America/Denver' // 比UTC晚6个小时
});
```

## 限制

`toLocaleString()`函数对于简单的、人类可读的格式化已经满足使用，但它不能提供`moment`那种级别的控制。

例如，`toLocaleString()`不能输出`YYYYMMDD`格式的日期，例如'20190601'。它可以输出`YYYY-MM-DD`格式的日期，但环境中必须有合适的区域。'en-US'区域不能输出YYYY-MM-DD格式，但'fr-CA'可以。

```javascript
const date = new Date('2019-06-01T00:00:00.000');
// "June 01, 2019, 2 AM"
date.toLocaleDateString('fr-CA', {
  year: 'numeric',
  month: '2-digit',
  day: '2-digit'
});
```

不好的是，猜到正确的区域需要试错。如果你需要比`'June 1, 2019'`更强好的日期格式化，你可以使用moment。

> 译注：moment.js已停止更新，moment.js官方建议使用day.js等“现代”的库。

