---
title: 使用ES6的Symbol原生实现JavaScript接口
date: 2021-05-11 16:52:55
categories:
tags:
---

翻译了2017年的一篇文章，介绍如何用Symbol实现JavaScript接口。

<!-- more -->

> 原文：[Interfaces in JavaScript with ES6 Symbol. Naive implementation](https://codeburst.io/interfaces-in-JavaScript-with-es6-naive-implementation-91b703110a09)

众所周知，JavaScript用独特的方式实现了OOP（面向对象），与C#和Java的实现方式非常不同。在ES6之前，JavaScript是不支持类（`class`）的，因此OOP是通过原型（`prototype`）和其他一些自定义的方式实现的。现在`class`类已经是JavaScript语言的一部分，而且逐渐被广泛使用，人们开始想要更进一步，在JavaScript中使用接口（`interface`）。为了满足这个需求，有人提出了[first class protocols](https://github.com/michaelficarra/proposal-first-class-protocols)。但这一提案仍处于Stage 1，所以距离成为语言标准还有很长的时间。我们在JavaScript的现状下，如何实现这一目的呢：

1. *不要简单的使用**鸭子类型**去实现*。有时候，这种方式也满足使用，但在许多情况下会导致应用发生不可预测的问题。

2. *使用接口编译库*。目前已经有许多库可以在JavaScript代码中实现接口，但我不满足于这种方式，因为我们的项目已经确定了不依赖任何第三方库。

3. *使用TypeScript*。Typescript作为JavaScript的超集，原生支持接口，但对我们的项目而言，切换到TypeScript成本太大。

4. *使用代理（`Proxy`）*。不幸的是，Babel不支持Proxy语法：`由于ES5的限制，代理无法被转译。详见不同JavaScript引擎的支持情况`。

5. *使用Symbol数据类型*。ES6标准增加了新的`Symbol`数据类型，具有非常重要的特性：

`Symbol()返回的每个symbol的值都是唯一的`

基于这一特性，我们的项目形成了一种实现接口的方式。

我们在类中，添加一个属性，以`Symbol`作为属性名，返回一个带有接口实现的对象。使用`Symbol`可以防止对象中已有的属性与接口属性命名冲突。

``` javascript myInterface.js
const myInterface = Symbol('myInterface');
 
export default myInterface;
```

``` javascript Foo.js
import myInterface from 'myInterface';
 
class Foo {
    constructor(value) {
        this.value = value;
    }
 
    _inc () {
        this.value = this.value + 1;
    }
 
    _getValue () {
        return this.value;
    }
 
    get [myInterface]() {
        return {
            inc: this._inc,
            get value() {
               return this._getValue();
            }
        };
    }
}
 
export default A;
```

``` javascript usage.js
import myInterface from 'myInterface';
import Foo from 'foo';

function doInc(obj){
  const interface = obj[myInterface];

  if (interface === undefined) {
    throw new Error('Object does not support the interface.');
  }
  interface.inc();
}

const counter = new Foo(0);

doInc(counter);
```

哈哈，非常原生的实现，而且可以检查对象是否实现了接口。我们可以更进一步，把接口中的每个函数以`Symbol`命名，但最终决定还是只在接口名使用`Symbol`。

实际上，ES6的生成器也是用这种方式，可以在任何对象中实现迭代器/生成器：

``` javascript object.with.generator.js
const objWithIterator = { 
  [Symbol.iterator]() {
      return {
          next: function() {
            console.log('do next');
            return {done: false, value: 'foo'}; 
          },
          return: function() {
            console.log('do return');
            return {done: true, value: 'foo'}; 
          },
          throw: function() {
            console.log('do throw');
            throw new Error('foo'); 
          }
      };
   }
}

objWithIterator[Symbol.iterator]().next();
// do next
// {done: false, value: "foo"}
```