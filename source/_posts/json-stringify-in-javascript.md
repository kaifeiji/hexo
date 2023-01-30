---
title: JavaScript的JSON.stringify()
date: 2023-01-30 17:23:34
categories:
- Mastering JS
tags:
- JSON.stringify
---

原文：[JSON.stringify() in JavaScript](https://masteringjs.io/tutorials/fundamentals/stringify)

`JSON.stringify()`函数是JavaScript对象转JSON字符串最常见的方法。本文从实用的角度、以示例说明`JSON.stringify()`。

<!-- more -->

[`JSON.stringify()`函数](http://thecodebarbarian.com/the-80-20-guide-to-json-stringify-in-javascript.html)主要用于JavaScript对象转[JSON字符串](https://www.json.org/)。几乎每个操作HTTP请求和响应的npm包，例如[axios](https://www.npmjs.com/package/axios)或[express](http://expressjs.com/)，都会在内部使用`JSON.stringify()`。

## JSON转换

`JSON.stringify()`的[第一个参数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify#Parameters)是要转换为JSON的值。

```javascript
const obj = { answer: 42 };
const arr = ['hello', 'world'];

typeof JSON.stringify(obj); // 'string'

JSON.stringify(obj); // '{"answer":42}'
JSON.stringify(arr); // '["hello","world"]'
```

JSON只能表示以下类型的值：

* Strings
* Numbers
* Objects
* Arrays
* Booleans
* null

读者可能注意到了列表中不包括一些[JavaScript的内置类型](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#Data_types)。确切的说，JSON不能表示JavaScript的`undefined`、`Symbol`和[`BigInt`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/BigInt)。`JSON.stringify()`会忽略`undefined`和`Symbol`类型的值，而不报错。

```javascript
let obj = { undef: undefined, symbol: Symbol('foo') };

// JSON.stringify() 忽略 `undefined` 和 `Symbol`
JSON.stringify(obj); // '{}'
```

`JSON.stringify()`如果发现BigInt值会抛出异常。

```javascript
// TypeError: Do not know how to serialize a BigInt
JSON.stringify({ val: 42n });
```

列表中也不包括一些JavaScript对象，例如[日期类型](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)。`JSON.stringify()`函数会将[日期转换为字符串](https://masteringjs.io/tutorials/fundamentals/date_format)。

```javascript
// '{"date":"2019-06-01T00:00:00.000Z"}'
JSON.stringify({ date: new Date('2019-06-01') });
```

## 替换函数

`JSON.stringify()`的第二个参数是替换函数。JavaScript为对象中的每个键值对调用该函数，并以返回值作为值进行处理。如果替换函数返回了`undefined`，JSON输出将不包含这个key。

例如，要用替换函数去掉`null`值：

```javascript
const obj = { answer: 42, test: null };
// '{"answer":42}'
JSON.stringify(obj, function replacer(key, value) {
  if (value === null) {
    return undefined;
  }
  return value;
});
```

## 美化格式

`JSON.stringify()`的第三个参数是缩进符。该参数是一个字符串或数值，JavaScript会按参数将JSON输出为人类可读的格式。如果指定了缩进符，JavaScript会把每个键值对单独一行，并在每一行用缩进符进行缩进。

```javascript
const obj = { firstName: 'Jean-Luc', lastName: 'Picard' };
// {
//   "firstName": "Jean-Luc",
//   "lastName": "Picard"
// }
JSON.stringify(obj, null, '  ');
// 等效，JavaScript认为 `2` 等价于 2 个空格。
JSON.stringify(obj, null, 2);
```