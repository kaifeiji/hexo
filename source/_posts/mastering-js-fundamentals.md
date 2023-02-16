---
title: Mastering JS - Fundamentals
date: 2023-01-02 15:52:30
categories:
- Mastering JS
tags:
- Mastering JS
- JavaScript
top: true
---

> Mastering JS创建于2019年4月，旨在帮助JavaScript开发者解决日常问题。市面上，太多千篇一律的从零构建APP，或者钻牛角尖的八股文。Mastering JS提供了简洁的教程和有效的工具，帮助开发者快速的解决问题。
> Mastering JS是[Valeri Karpov](https://twitter.com/code_barbarian)，[Mongoose](https://mongoosejs.com/)的维护者，在美国弗罗里达州迈阿密滩创建的。Mastering JS属于MeanIT Software的产品之一，MeanIT Software是聚焦于JavaScript的软件发行商。

<!-- more -->

## 译序

[**掌握JavaScript-基础篇**（Mastering JS - Fundamentals）](https://masteringjs.io/fundamentals)由一系列的小专题文章组成，每篇文章都是从JavaScript的某一个特性出发，讲解它的用法、优缺点、替代品等。

在本系列中比较有价值的内容：

* 从使用的角度，讲解容易忽略、容易出错的点
* JavaScript语言本身存在的一些缺陷，及JavaScript后续的发展
* 引出一些其他知识，例如相关的npm包、业界趣闻等
* 文章中间的链接，有一些水平高、有深度的文章（英文），值得深入研究

对有经验的开发者而言，有[MDN文档](https://developer.mozilla.org/zh-CN/)就够了，本系列文章的绝大部分内容都可以在其中找到。

但对初学者和经验不足的开发者，MDN太庞大而找不到重点。

而本系列文章是从语言的某一个切面出发，读者可以选择感兴趣的部分进行阅读。

单个文章篇幅几百字左右，即使加上代码测试，也不会超过10分钟，特别适合碎片时间阅读。

从今天开始，每天翻译一篇**掌握JavaScript-基础篇**的文章。

目前有180多篇文章，而且原网站也在持续更新，一天一篇，翻译完大概需要半年时间。

## 文章列表

[JavaScript如何比较两个Object](/post/how-to-compare-objects-in-javascript/)

> JavaScript中有很多方法来比较对象。本文提供了JavaScript比较对象的三种方法，以及它们的优缺点。

---

[JavaScript的switch/case语句](/post/the-switch-case-statement-in-javascript/)

> switch语句就像多个叠加的if语句，可以在不同条件执行不同的代码。本文展示了如何使用switch/case语句。

---

[JavaScript的this](/post/this-in-javascript/)

> `this`关键字，也称为函数的“上下文”或“范围”，是一个强大的也令人迷惑的概念。本文帮你捋清思路。

---

[JavaScript的===和==](/post/triple-equals-vs-double-equals-in-javascript/)

> 何时使用`==`，何时使用`===`，经常会导致开发者困惑不已。简单的版本：除了一个小小的例外，应该一直使用`===`。

---

[JavaScript获取明天的日期](/post/get-tomorrow-s-date-in-javascript/)

> 虽然用Moment.js一类的库很容易操作日期，但原生JavaScript也能获取明天日期。

---

[JavaScript的静态属性和函数](/post/static-properties-and-functions-in-javascript/)

> `static`关键字可以在ES6的类中定义静态属性和函数。

---

[JavaScript的取余运算符](/post/the-modulus-operator-in-javascript/)

> JavaScript的取余运算符返回第一个数除以第二个数的余数。

---

[JavaScript的真值](/post/truthy-values-in-javascript/)

> JavaScript的if条件不一定是Boolean值，可以是任何类型的值。内置的强制类型转换接收到一个真值，会转换为true。

---

[JavaScript的逻辑运算符](/post/logical-operators-in-javascript/)

> JavaScript有3个逻辑运算符：`&&`、`||`和`!`。这三个运算符常用在`if`语句，本文介绍了逻辑运算符其他的一些神奇的用法。

---

[JavaScript中的“假”值是什么](/post/what-is-falsy-in-javascript/)

> JavaScript的类型强制转换导致代码很容易出错，但如果理解了基本原理，可以省去很多无用的类型检查。本文简述了“假”值。

---
[JavaScript数组的filter()](/post/array-filter-in-javascript/)

> JavaScript的数组函数filter()可以筛选出符合指定条件的数组元素，本文简述该函数的使用。

---

[让JavaScript函数暂停一段时间](/post/making-a-javascript-function-sleep/)

> JavaScript没有内置的`sleep()`函数，但也可以通过其他方式让函数暂停一段时间。

---

[CORS（跨域资源共享）的简单总结](/post/a-brief-overview-of-cross-origin-resource-sharing-cors/)

> 跨域资源共享，简写为CORS，是浏览器端的一项协议，用于确定从其他域请求一个资源是否安全。本文简要介绍。

---

[POJO（Plain Old JavaScript Object）是什么](/post/what-is-a-plain-old-javascript-object-pojo/)

> POJO是JavaScript存储用户输入数据最常用的方式，如何确切定义POJO呢？

---

[JavaScript中Promise的then()函数](/post/the-promise-then-function-in-javascript/)

> `then()`函数是JavaScript的Promise交互的主要方式，是Promise链式语法的一部分。

---

[JavaScript的Promise](/post/promises-in-javascript/)

> Promise是一个代表异步操作的对象，它是JavaScript的基础并发原语。本文讲解了相关知识。

---

[JavaScript的instanceof操作符](/post/the-instanceof-operator-in-javascript/)

> `instanceof`运算符可以确定一个对象是否是一个类的实例。

---

[JavaScript的类](/post/classes-in-javascript/)

> 从ES6/2015开始，JavaScript新增了面向对象编程的内置关键字`class`。本文介绍了它的工作原理。

---

[JavaScript的JSON.stringify()](/post/json-stringify-in-javascript/)

> `JSON.stringify()`函数是JavaScript对象转JSON字符串最常见的方法。本文从实用的角度、以示例说明`JSON.stringify()`。

---

[JavaScript的Object.seal()](/post/object-seal-in-javascript/)

> `seal()`函数可以防止对象添加或移除属性，同时仍然允许修改已有的属性，本文介绍了一些相关知识。

---

[JavaScript对象的原型（prototype）简介](/post/intro-to-object-prototypes-in-javascript/)

> JavaScript的继承是基于原型（prototype-based）的，即使使用ES6中类的`extends`关键词时也是如此。本文介绍了原型（prototype）的一些知识。

---

[JavaScript的深拷贝和浅拷贝](/post/deep-copy-vs-shallow-copy-in-javascript/)

> 在JavaScript中，经常会听说“深克隆”和“浅克隆”，也叫做浅拷贝和浅拷贝。本文介绍了其含义及示例。

---

[JavaScript中的Object.assign()](/post/object-assign-in-javascript/)

> `Object.assign()`函数将一个对象的属性赋给另一个对象，可以用于浅拷贝对象或一次性赋值多个属性。

---

[JavaScript拼接字符串3种方法](/post/3-ways-to-concatenate-string-in-javascript/)

> JavaScript中可以用`+`运算符、`Array#join()`函数和`String#concat()`函数拼接字符串。本文介绍了一些需要了解的知识。

---

[JavaScript字符串首字母大写](/post/capitalize-the-first-letter-of-a-string-in-javascript/)

> JavaScript中如何将“dog”转为“Dog”。

---

[JavaScript中void 0的作用](/post/what-does-void-0-do-in-javascript/)

> JavaScript中的void运算符是一个普遍的难点，它很少出现在教程中，但它有一些方便的用途。本文展示了void运算符最常见的一些用途。

---

[JavaScript的表达式](/post/what-is-a-javascript-expression/)

> 类似Vue的许多框架，允许在HTML中内嵌JavaScript表达式。什么是表达式？可以在表达式加入`if`语句吗？

---

[JavaScript数组的push教程](/post/javascript-array-push-tutorial/)

> push()方法向JavaScript数组的末尾添加一个元素。本文介绍push()是如何使用的。

---

[检查JS数组是否包含特定值](/post/check-if-a-js-array-contains-a-specific-value/)

> 检查JavaScript数组是否包含某个值，有两种常见方法：`includes()`和`indexOf()`。本文展示了如何使用二者，以及二者分别适用的场景。

---

[JavaScript的箭头函数](/post/arrow-function-in-javascript/)

> 箭头函数可以让编写函数更简洁，但有一些语法上的奇怪之处。本文描述了有效的使用箭头函数所需要了解的知识。

---

[用Object.freeze()使JavaScript对象不可变](/post/make-javascript-objects-immutable-with-object-freeze/)

> `const`关键字并不能防止对象属性的修改，而Object.freeze()函数可以实现，但会带有警告。

---

[Javascript中“use strict”的作用](/post/what-does-use-strict-do-in-javascript/)

> Javascript的“use strict”会开启严格模式。本文解释了何为严格模式。

---

[理解JavaScript的回调机制](/post/understanding-callbacks-in-javascript/)

> “回调”是JavaScript中较其他语言不同的几个概念之一。本文围绕回调帮助你缕清思路。

---

[JavaScript的Object.keys()函数概览](/post/an-overview-of-the-object-keys-function-in-javascript/)

> 介绍如何使用JavaScript的Object.keys()。

---

[理解JavaScript中的Array.splice()](/post/understanding-array-splice-in-javascript/)

> 如何使用`splice()`从JavaScript数组中添加和移除元素。

---

[JavaScript中substring、substr和slice的区别](/post/substring-vs-substr-vs-slice-in-javascript/)

> JavaScript有3种方法可以获取字符串的子串。本文介绍了`String#substring()`、`String#substr()`和`String#slice()`的区别。

---

[JavaScript替换字符串中所有给定子字符串](/post/replace-all-instances-of-a-string-in-javascript/)

> 学习如何替换JavaScript字符串中所有给定子字符串。

---

[JavaScript的数组排序](/post/sorting-an-array-in-javascript/)

> 学习如何使用JavaScript内置的Array#sort()函数。

---

[JavaScript的Map](/post/maps-in-javascript/)

> 学习如何使用ES6的Map。

---

[JavaScript比较两个字符串](/post/compare-two-strings-in-javascript/)

> 学习如何比较两个字符串：哪个更大、更小或相等。

---

[JavaScript检查字符串是否包含子字符串](/post/check-if-a-string-contains-a-substring-in-javascript/)

> 学习使用原生JavaScript检查字符串是否包含子字符串。

---

[JavaScript中forEach()8个简洁的例子](/post/8-neat-examples-with-foreach-in-javascript/)

> 学习如何使用JavaScript的forEach()遍历嵌套数组、对象的key和其他类型。

---

[JavaScript中in和hasOwnProperty的区别](/post/the-difference-between-in-and-hasownproperty-in-javascript/)

> JavaScript中检查对象中是否包含指定key的两种方法。

---

[使用原生JavaScript格式化日期](/post/format-dates-using-vanilla-javascript/)

> 学习使用`toLocaleString()`函数格式化日期，不用外部库。

---

[JavaScript计算标准差](/post/calculate-standard-deviation-in-javascript/)

> 在Node.js和浏览器中使用Math.js计算标准差。

---

[理解JavaScript中的valueOf()](/post/understand-valueof-in-javascript/)

> 学习String类型、Number类型、Date类型都具备的valueOf()函数。

---

[JavaScript获取当前时间戳](/post/get-the-current-timestamp-in-javascript/)

> 学些如何在JavaScript中获取当前Unix时间戳。

---
