---
title: JavaScript的取余运算符
date: 2023-02-09 21:00:34
categories:
- Mastering JS
hide: true
tags:
- Modulus
---

原文：[The Modulus Operator in JavaScript](https://masteringjs.io/tutorials/fundamentals/modulus)

JavaScript的取余运算符返回第一个数除以第二个数的余数。

<!-- more -->

[取余运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Remainder)，返回第一个数除以第二个数的余数。它也可以叫做取模运算符，虽然[技术上取模运算符是另一个不同的概念](https://stackoverflow.com/questions/13683563/whats-the-difference-between-mod-and-remainder)。

取余运算符一个简单的示例：一商品价格3元，你有7元，你最多买2件，最终余1元。取余运算符`a % b`表达的是，如果你有`a`元，你尽可能多的购买价格为`b`的商品，你最终会剩下的钱。

```javascript
7 % 3; // 1
```

在英语中，`a % b`念做[“a mod b”](https://www.quora.com/What-does-a-mod-b-mean)。

取余运算符有几种用途：

## 数字的奇偶

一个整数可以被`2`整除，那么它是[偶数](http://mathworld.wolfram.com/EvenNumber.html)。取余运算符在`a`被`b`整除时返回`0`。那么，检查一个数字是不是偶数，也就相当于检查`n % 2 === 0`是否成立。

```javascript
function isEven(n) {
  return n % 2 === 0;
}

isEven(5); // false
isEven(7.6); // false
isEven(8); // true
```

相似的，如果`n % 2 === 1`，那么`n`是[奇数](http://mathworld.wolfram.com/OddNumber.html)。

```javascript
function isOdd(n) {
  return n % 2 === 0;
}

isEven(5); // false
isEven(7.6); // false
isEven(8); // true
```

## 数值的[小数部分](https://en.wikipedia.org/wiki/Fractional_part)

一个数值`2.5`，要获得小数点后的部分`0.5`。最简洁的方法是用这个数`% 1`。

```javascript
function getFractionalPart(n) {
  return n % 1;
}

getFractionalPart(2.5); // 0.5
```

## 分钟转换为小时

面对基本的日期时间计算，你又不想引入很重的第三方库，取余运算符就能派上用场了。例如，有一个数值`n`代表分钟数（可能大于60分钟），要把它转换为小时和分钟，可以这样做：

```javascript
const minutesToHoursAndMinutes = n => ({
  hours: Math.floor(n / 60),
  minutes: n % 60
});

minutesToHoursAndMinutes(59); // { hours: 0, minutes: 59 }
minutesToHoursAndMinutes(62); // { hours: 1, minutes: 2 }
minutesToHoursAndMinutes(123); // { hours: 2, minutes: 3 }
```

## 取余？取模？

模是要给相关的概念，但在处理负值时不太一样。例如，`-21 % 5 === -1`，因为余数总是取左侧值的符号。然而真正的取模运算符总是返回一个非负数，-21取模5等于4。实践中，很少会出现对负值取余数，所以许多JavaScript开发者没有意识到其中的区别。
