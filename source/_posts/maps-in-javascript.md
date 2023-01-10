---
title: JavaScript的Map
date: 2023-01-10 19:42:36
categories:
- Mastering JS
tags:
- Map
---

学习如何使用ES6的Map。

<!-- more -->

JavaScript的[Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)是一种存储key/value对的数据类型。可以使用`get()`和`set()`获取、设置key对应的value，或使用`has()`检查Map中是否有指定的key。

```javascript
const map = new Map();

map.get('answer'); // undefined
map.has('answer'); // false

map.set('answer', 42);

map.get('answer'); // 42
map.has('answer'); // true
```

ES6之前，JavaScript的映射类型一般用对象实现。Map在存储数据方面，比对象有几个优势。首先，对象有一些特殊的属性，可能与key名称产生冲突，一不小心，就会造成[原型中毒安全漏洞](https://medium.com/intrinsic/javascript-prototype-poisoning-vulnerabilities-in-the-wild-7bc15347c96)。因此在把对象用作映射类型时，要小心的[使用in或者hasOwnProperty()](https://masteringjs.io/tutorials/fundamentals/hasownproperty)。

```
const obj = {};
const map = new Map();

obj.answer = 42;
map.set('answer', 42);

'answer' in obj; // true
map.has('answer'); // true

'prototype' in obj; // true
map.has('prototype'); // false

'__proto__' in obj; // true
map.has('constructor'); // false
```

Map可以存储任意类型的key，而对象只能存储字符串类型的key。例如Map可以用[JavaScript的Date类型](https://masteringjs.io/tutorials/fundamentals/timestamps)作为key，而在对象中以Date类型作为key，会先转换成字符串。

```javascript
const map = new Map();

const date = new Date('2019-06-01');

map.set(date, 'test1');
map.set(date.toString(), 'test2');

map.get(date); // 'test1'
map.get(date.toString()); // 'test2'

const obj = {};
obj[date] = 'test1';
obj[date.toString()] = 'test2';
obj[date]; // 'test2', 因为JavaScript会将key转换为字符串
```

## Map构造函数

`Map`的构造函数接收一个参数`iterable`（可迭代的）。在实践中，`iterable`通常是key/value对的数组`[[key1, value1], [key2, value2]]`。也可以向Map构造函数传入一个`Map`对象。

```javascript
const date = new Date('2019-06-01');
const map1 = new Map([
  ['answer', 42],
  [date, 'test1']
]);

map1.get('answer'); // 42
map1.get(date); // test1

// 如果将 `map1` 传入Map构造函数, JavaScript会创建一个 `map1` 的拷贝
const map2 = new Map(map1);
map2.get('answer'); // 42
map2.get(date); // test1

map2.set('hello', 'world');
map1.get('hello'); // undefined
```

可以向Map构造函数传入一个对象。要把一个对象转换成Map，应该[使用Object.entries()函数将对象转换为key/value对的数组](https://masteringjs.io/tutorials/fundamentals/foreach#example-4-object-keys-and-values)。

```javascript
const obj = { answer: 42 };
// 这里会抛出错误，因为对象**不是**可迭代的
// "TypeError: undefined is not a function"
new Map(obj);

// 这里正常运行, 使用 `Object.entries()` 把对象转换为key/value对数组
const map = new Map(Object.entries(obj));
map.get('answer'); // 42
```

## 遍历Map

可以使用`for/of`循环遍历Map的key或key/value对。Map的[keys()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map/keys)函数返回Map的key数组，[entries()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map/entries)函数返回Map的key/value对数组。

```javascript
const map = new Map([
  ['key1', 1],
  ['key2', 2],
  ['key3', 3]
]);

for (const key of map.keys()) {
  // 'key1', 'key2', 'key3'
  key;
}

for (const [key, value] of map.entries()) {
  // 'key1', 'key2', 'key3'
  key;
  // 1, 2, 3
  value;
}
```