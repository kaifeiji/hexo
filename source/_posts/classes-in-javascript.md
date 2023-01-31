---
title: JavaScript的类
date: 2023-01-31 09:06:44
categories:
- Mastering JS
tags:
- 类
---

原文：[Classes in JavaScript](https://masteringjs.io/tutorials/fundamentals/class)

从ES6/2015开始，JavaScript新增了面向对象编程的内置关键字`class`。本文介绍了它的工作原理。

<!-- more -->

在面向对象编程中，[类](https://brilliant.org/wiki/classes-oop/)是创建对象的模板。[JavaScript的`class`关键字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)可以声明一个新的类。

```javascript
class Rectangle {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
}

const obj = new Rectangle(3, 5);
obj.height; // 3
obj.width; // 5

// `instanceof` 关键字检查一个对象是否由某个类创建
obj instanceof Rectangle; // true
({}) instanceof Rectangle; // false
```

## 方法

[方法](https://brilliant.org/wiki/methods-oop/)就是定义在类中的函数，JavaScript会将方法添加到每个类的实例中。例如，要计算`Rectangle`的面积，可以定义一个`area()`方法，如下：

```javascript
class Rectangle {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }

  // 要定义一个 `methodName` 方法，在类声明中加入 `methodName() {}`
  area() {
    return this.width * this.height;
  }
}

const obj = new Rectangle(3, 5);
obj.area(); // 15
```

在这个方法中，[`this`关键字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this)指向了方法所属的类的实例。以上代码中的`this`指向`obj`。

Statics
A static is a a function that is defined on the class itself. In JavaScript, a class is just another variable, so you can call static functions on a class.
## Static

[`static`](https://beginnersbook.com/2013/05/static-vs-non-static-methods/)关键字代表函数是定义到类本身的。JavaScript中，一个类也是一个变量，可以直接调用类的[静态函数](https://masteringjs.io/tutorials/fundamentals/static)。

```javascript
class Rectangle {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }

  // 要定义一个静态函数 `functionName`在类声明中加入 `static functionName() {}`
  static createSquare(length) {
    return new Rectangle(length, length);
  }
}

const obj = Rectangle.createSquare(5);
obj.height; // 5
obj.width; // 5
```

## getter/setter

定义`Rectangle`的`area`，可以用另一种方法——[`getter`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get)。使用`getter`，不使用方法，就能创建一个动态计算的`Rectangle`的`area`属性。

```javascript
class Rectangle {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }

  // 要定义一个getter `getterName`在类声明中加入 `get getterName() {}`。注意：getter是函数！
  get area() {
    return this.height * this.width;
  }
}

const obj = new Rectangle(3, 5);
obj.area; // 15
```

同样，也可以定义一个`setter`，在设置某个属性时进行调用。例如，需要确定`height`和`width`都是数值型，可以定义`setter`，在设置为非数值型时抛出异常。

```javascript
class Rectangle {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }

  get height() {
    return this._height;
  }

  set height(v) {
    assert.ok(typeof v === 'number', 'height必须为数值型');
    return v;
  }

  get width() {
    return this._width;
  }

  set width(v) {
    assert.ok(typeof v === 'number', 'width必须为数值型');
    return v;
  }
}

const obj = new Rectangle(3, 5);
// 抛出 'AssertionError: height必须为数值型'
obj.height = 'Not a number';
```

## 继承

当[一个类继承另一个类](https://thecodebarbarian.com/an-overview-of-es6-classes#inheritance)，意味着子类默认具有所有父类的静态方法、方法、`getter`和`setter`。而子类可以定义额外的静态方法、方法、`getter`和`setter`，子类还可以[覆盖](https://en.wikipedia.org/wiki/Method_overriding)基类的静态方法、方法、`getter`和`setter`。

```javascript
class Rectangle {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }

  area() {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
  constructor(length) {
    // `super` 指向基类的 constructor
    super(length, length);
  }

  // 内切圆半径
  // http://mathworld.wolfram.com/Square.html
  inradius() {
    return this.height / 2;
  }
}

const obj = new Square(5);

obj.area(); // 25, 来源于 `Rectangle`基类
obj.inradius(); // 2.5, 来源于 `Square` 类

obj instanceof Square; // true
obj instanceof Rectangle; // true
```

## 继承仍然是基于原型的

关键字`extends`在底层仍然使用[基于原型的继承](https://masteringjs.io/tutorials/fundamentals/prototype)。也就是说，可以在ES6的类中，混合使用原型模式。

```javascript
class Rectangle {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
}

Rectangle.prototype.area = function area() {
  return this.width * this.height;
};

const obj = new Rectangle(3, 5);

obj.area(); // 15
```