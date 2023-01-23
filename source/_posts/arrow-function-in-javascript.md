---
title: JavaScript的箭头函数
date: 2023-01-19 11:18:42
categories:
- Mastering JS
tags:
- 箭头函数
---

原文：[Arrow Functions in JavaScript](https://masteringjs.io/tutorials/fundamentals/arrow)

箭头函数可以让编写函数更简洁，但有一些语法上的奇怪之处。本文描述了有效的使用箭头函数所需要了解的知识。

<!-- more -->

为了解决传统函数一些常见的问题，[箭头函数](https://exploringjs.com/es6/ch_arrow-functions.html)从ES6开始引入。但是由于[一些场景箭头函数并不适用](https://mongoosejs.com/docs/faq.html#arrow-functions)，因此仍要了解何时使用传统函数，何时使用箭头函数。

## 语法

出现`=>`符号时，就代表这是一个箭头函数。有两种方式声明一个箭头函数：

1、不带花括号`{}`。这种语法中，箭头函数有隐式的返回值。例如，如下箭头函数虽然没有`return`关键字，但它会返回42。

```javascript
// 'getAnswer()` is an arrow function that returns 42
const getAnswer = () => 42;

getAnswer(); // 42
```

2、带花括号`{}`。这种语法中，箭头函数不是隐式的返回。

```javascript
// 'getAnswer()` is an arrow function that returns 42
const getAnswer = () => {
  return 42;
};

getAnswer(); // 42
```

箭头函数返回一个对象字面量时，有一些麻烦：

```javascript
// 语法错误！JavaScript认为 `=>` 后的花括号是代码块，而非对象
const getObj = () => { answer: 42 };

// 用圆括号括起来的对象字面量，函数可以正确返回一个对象，`obj.answer = 42`
const getObj = () => ({ answer: 42 });

getObj(); // 42
```

不带花括号时，在箭头`=>`右侧只能放一个[表达式](https://2ality.com/2012/09/expressions-vs-statements.html)。直观的看，这意味着在“单行程序”中只能使用不带花括号的语法。可以使用三元操作符`?:`、`&&`和`||`，但不能使用`if`语句或分号。

```javascript
let answer = 42;
// 不带花括号，箭头函数只能包含一个表达式。
// 以下函数运行正常，不带花括号不能使用 `if` 语句。
const getAnswer = () => answer !== null && answer !== undefined ?
  answer :
  0;

getAnswer(); // 42
```

## 参数

和普通函数一样，箭头函数可以接收0个或多个参数。除了只有一个参数的箭头函数，必须把参数名放在圆括号中，`(param1, param2, param3) => {}`。

```javascript
// 如果箭头函数没有参数，必须带圆括号 `() =>`
const getAnswer = () => 42;

// 如果箭头函数只接收1个参数，可以省略参数的圆括号
let noop = v => v;
// 以下是等效的：
noop = (v) => v;

// 如果箭头函数接收多于1个的参数，必须在参数名外加圆括号
const add = (a, b) => a + b;
```

## 何时使用箭头函数？

箭头函数有两个主要的优势：

1、单行函数的隐式返回使代码更简洁

2、[词法this](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions#No_separate_this)。箭头函数中的`this`与箭头函数外的this指向同一个对象。

例如，在`setTimeout()`中调用一个[类方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Method_definitions)。如果不使用箭头函数，使用普通函数，`this`**不是**`MyClass`的实例。

```javascript
class MyClass {
  constructor(message) {
    this.message = message;
  }

  print() {
    setTimeout(function() {
      // undefined, 因为 `this` 是 `setTimeout()` 回调中的 `Timeout` 对象
      this.message;
    }, 100);
  }
}

const obj = new MyClass('Hello, World');
obj.message; // 'Hello, World'
obj.print();
```

使用箭头函数，`this`是`MyClass`的一个实例。

```javascript
class MyClass {
  constructor(message) {
    this.message = message;
  }

  print() {
    setTimeout(() => {
      // 'Hello, World'
      this.message;
    }, 100);
  }
}

const obj = new MyClass('Hello, World');
obj.message; // 'Hello, World'
obj.print();
```

## 何时不使用箭头函数？

箭头函数很好用，而且很多情况箭头函数和普通函数都没问题。但在使用依赖`this`的框架中，不能使用箭头函数。

例如，假设用箭头函数声明一个[Vue方法](https://v1.vuejs.org/guide/events.html#Method-Handler)，会无法访问Vue实例的`name`属性，因为Vue无法设置`this`。

```javascript
const Vue = require('vue');

const app = new Vue({
  data: () => ({ name: '' }),
  // 这个方法 **无法** 正常运行. Vue方法需要正确的 `this`
  methods: {
    setName: newName => this.name = newName
  },
  template: `
    <div>
      <h1>{{name}}</h1>
      <button v-on:click="setName('Hello')"></button>
    </div>
  `
});
```

还有一个常见的例子就是[Mocha的timeout](https://mochajs.org/#test-level)。在[Mocha测试](https://masteringjs.io/tutorials/mocha/intro)中使用箭头函数，无法正确的进行timemout测试。

```javascript
describe('MyFunction', () => {
  it('works', () => {
    this.timeout(500); // 会抛出错误
  });
});
```

一般来说，**不能**向框架传递箭头函数，除非可以确定不用`this`关键字。例如，在Vue方法、Mocha测试、React类方法或[Mongoose模型方法](https://mongoosejs.com/docs/guide.html#methods)中，不要使用箭头函数。可以在Vue方法、Mocha测试的内部使用箭头函数，但向Vue或Mocha输入的顶层函数**不能**是箭头函数。
