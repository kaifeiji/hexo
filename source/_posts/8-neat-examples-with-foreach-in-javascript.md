---
title: JavaScript中forEach()8个简洁的例子
date: 2023-01-07 13:32:28
categories:
- Mastering JS
tags:
- forEach
- 遍历
---

学习如何使用JavaScript的forEach()遍历嵌套数组、对象的key和其他类型。

<!-- more -->

[Array#forEach()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)函数是遍历数组的一种常用方法。然而，结合其他的语言特性，`forEach()`可以做的，远不止“打印数组中每个值”。本文提供了8个例子，演示`forEach()`的常用模式。

## 例1：基础

`forEach()`函数的第一个参数是一个回调函数，JavaScript会对数组中每个元素执行该函数。

```javascript
// Prints "a", "b", "c"
['a', 'b', 'c'].forEach(v => {
  console.log(v);
});
```

## 例2：修改数组

一般而言，使用`forEach()`时不应该修改数组。如果想要修改数组，可以使用[Array#map()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)。但使用`forEach()`也是支持修改数组的，而且你有可能会看到有些代码这么做。以下是用`forEach()`将每个数组元素转换为大写的例子。

```javascript
const arr = ['a', 'b', 'c'];
arr.forEach((v, i) => {
  arr[i] = v.toUpperCase();
});
arr; // ['A', 'B', 'C']
```

## 例3：对象的key

[Object.keys()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/keys)函数返回一个包含对象key的数组。用`Object.keys()`，结合`forEach()`可以遍历对象的key。

```javascript
const obj = {
  a: 1,
  b: 2,
  c: 3
};

// 打印 "a", "b", "c"
Object.keys(obj).forEach(key => console.log(key));
```

## 例4：对象的key和value

用`forEach()`和`Object.keys()`可以遍历对象的key。但如何同时遍历key和value呢？就轮到[Object.entries()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/entries)函数登场了。`Object.entries()`可以返回一个对象的`[key, value]`数组对。

```javascript
const obj = {
  a: 1,
  b: 2,
  c: 3
};

// 打印 "a 1", "b 2", "c 3"
Object.entries(obj).forEach(([key, value]) => {
  console.log(key + ' ' + value)
});
```

## 例5：嵌套数组和flat()

`forEach()`函数只会遍历数组的第一级。如果有一个包含数组的数组，需要先使用[Array#flat()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/flat)将嵌套数组扁平化。

```javascript
const arr = ['a', ['b', 'c'], [['d', ['e']]]];

// 打印 "a", "b", "c", "d", "e". `3` 代表扁平化的最深层次
arr.flat(3).forEach(v => console.log(v));
```

## 例6：向数组添加元素

`forEach()`函数在第一次调用回调函数之前，就确定了遍历的元素。那么，如果在`forEach()`的回调函数中，向数组添加元素，JavaScript**不会**对新元素调用回调函数。不必担心在`forEach()`回调中向数组添加元素会导致无限循环。

```javascript
const arr = ['a', 'b', 'c'];

// 打印 "a", "b", "c", 虽然每次调用回调都向数组添加了一个新元素
arr.forEach(v => {
  arr.push(v.toUpperCase());
  console.log(v);
});
```

## 例7：thisArg

`forEach()`函数实际上接收2个参数，`callback`和`thisArg`。`thisArg`作用是在`callback`中设置`this`指向。`thisArg`参数对于依赖`this`的函数非常有用，比如下面例子中的`Stack`类。

```
const arr = ['a', 'b', 'c'];

class Stack {
  constructor() {
    this._arr = [];
  }

  push(v) {
    this._arr.push(v);
  }

  pop() {
    return this._arr.pop();
  }
}

const stack = new Stack();
// 没有 `thisArg`, 将会报错
arr.forEach(stack.push, stack);
// 等效于:
arr.forEach(v => stack.push(v));
// 也等效于:
arr.forEach(stack.push.bind(stack));
```

## 例8：数组空洞

JavaScript数组有很多怪异之处。确切的说，数组`['a',, 'c']`和`['a', undefined, 'c']`是不一样的，即使二者的`arr[1] === undefined`都成立。`['a',, 'c']`的`arr[1]`就称为[“数组空洞”](http://2ality.com/2015/09/holes-arrays-es6.html)。

`forEach()`函数会跳过数组空洞。为了让`forEach()`将数组空洞按`undefined`处理，需要[用Array.from()把数组空洞去掉](http://2ality.com/2015/09/holes-arrays-es6.html#arrayfrom)。

```javascript
const arr = ['a',, 'c'];

// 打印 "a", "c"
arr.forEach(v => console.log(v));

// 打印 "a", "undefined", "c". `Array.from()` 去掉了空洞
Array.from(arr).forEach(v => console.log(v));
```