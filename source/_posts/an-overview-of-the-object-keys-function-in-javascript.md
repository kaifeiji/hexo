---
title: JavaScript的Object.keys()函数概览
date: 2023-01-15 17:52:33
categories:
- Mastering JS
tags:
- Object.keys
---

原文：[An Overview of the Object.keys() Function in JavaScript](https://masteringjs.io/tutorials/fundamentals/keys)

介绍如何使用JavaScript的Object.keys()。

<!-- more -->

[Object.keys()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/keys)函数返回一个对象的属性名的数组。例如一个POJO对象`obj`，调用`Object.keys()`会返回对象所有的key。

```javascript
const obj = {
  name: 'Jean-Luc Picard',
  age: 59,
  rank: 'Captain'
};
Object.keys(obj); // ['name', 'age', 'rank']
```

## key的顺序

属性名是按照属性设置的顺序排列的。如果`delete`一个属性，然后再次设置该属性，它会位于数组的末尾。

```javascript
const obj = {
  name: 'Jean-Luc Picard',
  age: 59
};

obj.rank = 'Captain';
Object.keys(obj); // ['name', 'age', 'rank']

delete obj.age;
obj.age = 59;
Object.keys(obj); // ['name', 'rank', 'age']
```

[ECMAScript规范称之为“属性创建顺序”](http://www.ecma-international.org/ecma-262/6.0/#sec-ordinary-object-internal-methods-and-internal-slots-ownpropertykeys)。`Object.keys()`函数在[所有ES2015兼容的环境中，可以保证返回的key按属性创建顺序](https://www.stefanjudis.com/today-i-learned/property-order-is-predictable-in-javascript-objects-since-es2015/#the-internal-code-ownpropertykeys-code-method)。但有一个关键性的例外：数值型key。

任何属性名是介于`0`和`2^32 - 1`（包含）之间的整数，会位于所有非整数key之前，而且这些属性会按数值排序。

```javascript
const obj = {
  name: 'Jean-Luc Picard',
  rank: 'Captain',
  1: 'test',
  100: 'test',
  10: 'test'
};

Object.keys(obj); // ['1', '10', '100', 'name', 'rank']
```

## ES6的类

`Object.keys()`函数只返回所谓的[“自有属性”](https://masteringjs.io/tutorials/fundamentals/hasownproperty)。因此`Object.keys()`不会返回类方法或类属性。

```javascript
class Character {
  get show() { return 'Star Trek: The Next Generation'; }

  firstName() {
    return this.name.slice(0, this.name.indexOf(' '));
  }
}

const obj = new Character();
Object.assign(obj, {
  name: 'Jean-Luc Picard',
  age: 59,
  rank: 'Captain'
});

obj.show; // 'Star Trek: The Next Generation'
obj.firstName(); // 'Jean-Luc'

// `show` 和 `firstName` **不是** 自有属性, 因为它们来源于类
Object.keys(obj); // ['name', 'age', 'rank']

// 如果重写类属性, 它就会成为自有属性.
obj.firstName = () => 'test';
Object.keys(obj); // ['name', 'age', 'rank', 'firstName']
```

## Symbol

`Object.keys()`的属性**不包括**Symbol类型的属性名。需要用`Object.getOwnPropertySymbols()`来获取Symbol属性。

```javascript
const rankSymbol = Symbol('rank');

const obj = {
  name: 'Jean-Luc Picard',
  age: 59,
  [rankSymbol]: 'Captain'
};

Object.keys(obj); // ['name', 'age']
Object.getOwnPropertySymbols(obj); // [ Symbol(rank) ]
```