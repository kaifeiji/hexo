---
title: 如何遍历JavaScript的对象
date: 2023-02-23 18:08:30
categories:
- Mastering JS
hide: true
tags:
- 遍历
---

原文：[How to Iterate Over a JavaScript Object](https://masteringjs.io/tutorials/fundamentals/iterate-object)

有许多种方法可以遍历JavaScript对象的所有key和value。本文介绍3种方法和它们的优缺点。

<!-- more -->

有一个简单的JavaScript对象：

```javascript
const obj = {
  name: 'Luke Skywalker',
  title: 'Jedi Knight',
  age: 23
};
```

如何遍历key/value对，并打印“name: Luke Skywalker”、“title: Jedi Knight”和“age: 23”？现代JavaScript种有许多方法实现，本文介绍了3种不同的方法：

## [Object.entries()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/entries)

`Object.entries()`函数返回一个数组，其中包含对象的key/value对。使用`Object.entries()`和[`for/of`循环](https://thecodebarbarian.com/for-vs-for-each-vs-for-in-vs-for-of-in-javascript)，可以打印key/value对。

```javascript
const obj = {
  name: 'Luke Skywalker',
  title: 'Jedi Knight',
  age: 23
};

// 打印：
// 'name: Luke Skywalker'
// 'title: Jedi Knight'
// 'age: 23'
for (const [key, value] of Object.entries(obj)) {
  console.log(`${key}: ${value}`);
}
```

Object.entries(obj)返回的数组，其中每个都包含2个元素，第一个是`key`，第二个是`value`。这种模式在JavaScript中很常见，例如[Map的constructor](https://thecodebarbarian.com/the-80-20-guide-to-maps-in-javascript.html)和[`Object.fromEntries()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/fromEntries)。

如果用`Array.from()`将`Object.entries()`返回的迭代器转换为数组，数组中包含的就是对象的key/value对。

```javascript
const obj = {
  name: 'Luke Skywalker',
  title: 'Jedi Knight',
  age: 23
};

const keyValuePairs = Array.from(Object.entries(obj));
keyValuePairs[0]; // ['name', 'Luke Skywalker']
keyValuePairs[1]; // ['title', 'Jedi Knight']
keyValuePairs[2]; // ['age', 23]
```

## [Object.keys()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/keys)

`Object.keys()`函数返回对象key的数组，而不是key/value对。可以用方括号`[]`获取对象的value。

```javascript
const obj = {
  name: 'Luke Skywalker',
  title: 'Jedi Knight',
  age: 23
};

// 打印
// 'name: Luke Skywalker'
// 'title: Jedi Knight'
// 'age: 23'
for (const key of Object.keys(obj)) {
  console.log(`${key}: ${obj[key]}`);
}
```

有了`Object.entries()`为什么还要有`Object.keys()`？因为entry中的值是无法改变对象中的值的。

```javascript
const obj = {
  name: 'Luke Skywalker',
  title: 'Jedi Knight',
  age: 23
};

// 设置`value`不能改变属性值
// 需要设置`obj[key] = 新的值`
for (let [key, value] of Object.entries(obj)) {
  if (key === 'title') {
    value = 'Jedi Master';
  }
}
obj.title; // 'Jedi Knight'

// 可以修改！
for (const key of Object.keys(obj)) {
  if (key === 'title') {
    obj[key] = 'Jedi Master';
  }
}
```

## for/in

`Object.keys()`和`Object.entries()`只会循环[对象自身的属性](https://masteringjs.io/tutorials/fundamentals/hasownproperty)。对于[POJO](https://masteringjs.io/tutorials/fundamentals/pojo)而言，区别不大，但在遇到[继承](https://thecodebarbarian.com/an-overview-of-es6-classes#inheritance)的情况，可能会有一些问题。`for/in`循环可以遍历对象的所有key，包括继承的key。

```javascript
function JediKnight(name, age) {
  this.name = name;
  this.age = age;
}
// `JediKnight`类的实例会继承属性`title`
JediKnight.prototype.title = 'Jedi Knight';

const obj = new JediKnight('Luke Skywalker', 23);

// `entries`不包括`title`属性
const entries = Object.entries(obj);
entries; // [['name', 'Luke Skywalker'], ['age', '23']]

// 包括`title`因为`for/in`会遍历继承属性。
// 'name: Luke Skywalker'
// 'age: 23'
// 'title: Jedi Knight'
for (const key in obj) {
  console.log(`${key}: ${obj[key]}`);
}
```

一般来说，应该使用`Object.keys()`或`Object.entries()`遍历POJO，避免意外取到继承属性。但如果确实需要遍历继承属性，就需要用`for/in`了。
