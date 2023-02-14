---
title: JavaScript的===和==
date: 2023-02-11 16:52:47
categories:
- Mastering JS
tags:
- equal
---

原文：[Triple Equals vs Double Equals in JavaScript](https://masteringjs.io/tutorials/fundamentals/equals)

何时使用`==`，何时使用`===`，经常会导致开发者困惑不已。简单的版本：除了一个小小的例外，应该一直使用`===`。

<!-- more -->

在JavaScript中，技术上有[4种不同的方式可以比较两个值是否相等](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Equality_comparisons_and_sameness)。最常用的2种是[`==`运算符和`===`运算符](https://codeburst.io/javascript-showdown-vs-7be792be15b5)，也可以叫做宽松相等和[严格相等](https://www.ecma-international.org/ecma-262/10.0/index.html#sec-strict-equality-comparison)。

## === 严格相等

2个值`x`和`y`，JavaScript这样检查是否`x === y`：

1. 检查`x`和`y`的类型。如果是不同类型，返回false。
1. 如果`x`和`y`是数值，先检查`x`或`y`是不是`NaN`，其中一个是`NaN`则返回false。如果`x`和`y`是`+0`或`-0`，返回`true`。其他情况，检查它们是否是相同数值。
1. 如果`x`和`y`都是`null`或都是`undefined`，返回`true`。
1. 如果`x`和`y`都是`Boolean`、或`String`、或`Symbol`，根据值进行比较。
1. 如果`x`和`y`都是对象，只有在它们指向相同对象时才返回`true`。

简言之，`===`最重要的细节是：

1. 没有隐式的类型转换。`===`不会调用`valueOf()`或尝试用其他方式把对象转为原始类型。
1. 没有任何值`=== NaN`。不要用`===`检查NaN，用[Number.isNaN()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/isNaN)。
1. 对象比较是[按引用比较](http://adripofjavascript.com/blog/drips/object-equality-in-javascript.html)——两个不同的对象，包含完全相同的键值对，但`===`认为它们是不相等的。

```javascript
const obj1 = { answer: 42 };
const obj2 = { answer: 42 };

obj1 === obj2; // false
```

## 宽松相等 ==

`==`运算符使用更复杂的[抽象相等比较](https://www.ecma-international.org/ecma-262/10.0/index.html#sec-abstract-equality-comparison)算法，来比较`x`和`y`是否相等。总结如下：

1. 如果`x`和`y`是相同类型，检查`x === y`是否成立。
1. 如果`x`和`y`都是null或都是undefined，返回true。
1. 如果`x`是数值而`y`是字符串，[将`y`转换为数值](https://www.ecma-international.org/ecma-262/10.0/index.html#sec-tonumber)，然后用`===`比较。类似的，如果`x`是布尔值或字符串，而`y`是数值，则`x`转换为数值。
1. 如果`x`或`y`是布尔值，将另一个值转换为数值进行比较。
1. 如果`x`是对象，而`y`是Symbol、或字符串、或数值，尝试用valueOf()函数将`x`转换为原始类型，然后用`===`进行比较。

宽松相等会造成很多著名的[BUG场景](https://www.destroyallsoftware.com/talks/wat)。

```javascript
'    ' == 0; // true
'    ' == false; // true
({ valueOf: () => 42 }) == 42; // true
({ valueOf: () => 0 }) == false; // true
```

一般来说，应该总是用`===`，而非`==`，除非你有确切的理由。`==`有一个简洁的用途：一次检查是否为`null`或`undefined`（所谓的[空值](https://masteringjs.io/tutorials/fundamentals/falsy#nullish-values)）：

``` javascript
// 只有在`v === null`或`v === undefined`才是true
v == null;

// 等价于
v === null || v === undefined;
```

`v == null`是检查`v`是否严格等于`null`或`undefined`的一种简便方法，如果你是一个熟练的JavaScript开发者，这样使用是没问题的。但是，一直使用`===`也是没错的，而且更安全。

ESLint有一条规则，除非右侧是`null`，否则禁用`==`。
