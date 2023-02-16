---
title: JavaScript的switch/case语句
date: 2023-02-16 22:23:09
categories:
- Mastering JS
tags:
- switch
- case
---

原文：[The Switch/Case Statement in JavaScript](https://masteringjs.io/tutorials/fundamentals/switch-case)

`switch`语句就像多个叠加的if语句，可以在不同条件执行不同的代码。本文展示了如何使用`switch/case`语句。

<!-- more -->

[`switch`语句](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/switch)计算一个表达式，计算结果等于某个`case`的值时，就会执行这个`case`的语句。

```javascript
const hero = 'Batman';
let sidekick;

switch (hero) {
  case 'Batman':
    sidekick = 'Robin';
    break;
  case 'Aquaman':
    sidekick = 'Aqualad';
    break;
  case 'Superman':
    sidekick = 'Jimmy Olsen';
    break;
  default:
    throw new Error('Unknown hero');
}

sidekick; // 'Robin'
```

一定别忘了在语句块后加`break`！如果在`case`语句块最后没有`break`，JavaScript会继续执行下一个`case`。

```javascript
const hero = 'Batman';
let sidekick;

switch (hero) {
  case 'Batman':
    sidekick = 'Robin';
    // 如果没有break，会执行下一个case语句块
    // break;
  case 'Aquaman':
    sidekick = 'Aqualad';
    break;
  case 'Superman':
    sidekick = 'Jimmy Olsen';
    break;
  default:
    throw new Error('Unknown hero');
}

// 'Batman'和'Aquaman'语句块都会执行
// 得到的sidekick是错误的
sidekick; // 'Aqualad'
```

这个语言特点也有一些妙用，可以多个`case`条件执行同一个语句块。例如：

```javascript
const sidekick = 'Nightwing';
let hero;

switch (sidekick) {
  // 'Robin'和'Nightwing'的case属于是路过case语句。
  // 它们会执行和'Bluebird'的case相同的语句块。
  case 'Robin':
  case 'Nightwing':
  case 'Bluebird':
    hero = 'Batman';
    break;
  case 'Aqualad':
  case 'Tempest':
    hero = 'Aquaman';
    break;
  default:
    throw new Error('Unknown sidekick');
}

hero; // 'Batman'
```

## 相等检查

`switch`语句先计算给定表达式的值，然后用[严格相等](https://masteringjs.io/tutorials/fundamentals/equals#strict-equality-with-)来比较每个`case`的值。以下`if`语句在功能上与第一个例子是等效的：

```javascript
const hero = 'Batman';
let sidekick;

if (hero === 'Batman') {
  sidekick = 'Robin';
} else if (hero === 'Aquaman') {
  sidekick = 'Aqualad';
} else if (hero === 'Superman') {
  sidekick = 'Jimmy Olsen';
} else {
  throw new Error('Unknown hero');
}

sidekick; // 'Robin'
```

因为`switch`语句使用严格相等，要比较对象时要注意做好类型转换，例如[日期](https://masteringjs.io/tutorials/fundamentals/date_format)或[MongoDB的ObjectId](https://docs.mongodb.com/manual/reference/method/ObjectId/)。

```javascript
const date = new Date('2020/07/04');
let holiday;

const goodFriday = new Date('2020/04/10');
const independenceDay = new Date('2020/07/04');
const christmas = new Date('2020/12/25');

// 只有两个日期指向同一个对象，它们才是严格相等。
date === independenceDay; // false

// date是一个对象，因此要用getTime()转换为数值
// 否则所有的case都不会执行
switch (date.getTime()) {
  case goodFriday.getTime():
    holiday = 'Good Friday';
    break;
  case independenceDay.getTime():
    holiday = 'Independence Day';
    break;
  case christmas.getTime():
    holiday = 'Christmas';
    break;
}

holiday; // 'Independence Day'
```

## 替代方案

除了路过式的`case`语句，其他情况都可以用if语句来替代`switch/case`。另一种替代方案是定义一个对象或[Map](https://thecodebarbarian.com/the-80-20-guide-to-maps-in-javascript.html)，以`case`条件作为key，包含对应的函数：

```javascript
const hero = 'Batman';
let sidekick;

const obj = {
  'Batman': () => { sidekick = 'Robin'; },
  'Aquaman': () => { sidekick = 'Aqualad'; },
  'Superman': () => { sidekick = 'Jimmy Olsen'; }
};

// 要为对象使用hasOwnProperty()
// 否则constructor也会是一个有效的hero
if (obj.hasOwnProperty(hero)) {
  obj[hero]();
}

sidekick; // 'Robin'
```

用对象实现的条件分支，优势是能以编程方式构建对象。如果`switch`语句有点重复冗长，可以考虑用`for`循环，构建一个条件分支的对象。

## 建议

`switch`语句有许多不直观之处，例如意外的跳到下一个`case`语句。[ESLint有一个`no-fallthrough`规则](https://eslint.org/docs/rules/no-fallthrough)，可以在lint层规避这个问题。然而，大部分情况下`if/else`语句或对象条件都是更好的选择，因为`switch`比较少见，大多数人对`switch`的语义不敏感。
