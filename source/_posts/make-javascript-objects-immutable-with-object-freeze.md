---
title: 用Object.freeze()使JavaScript对象不可变
date: 2023-01-18 16:26:45
categories:
- Mastering JS
tags:
- Object.freeze
---

`const`关键字并不能防止对象属性的修改，而Object.freeze()函数可以实现，但会带有警告。

<!-- more -->

[JavaScript中的对象不可变](https://stackoverflow.com/questions/33124058/object-freeze-vs-const)有一些混乱之处。[ES6的`const`关键字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const)可以防止覆盖对象变量，但它**不能**防止对象属性的修改。

```javascript
const obj = { answer: 42 };

// 虽然 `obj` 以 `const` 声明, 仍然可以修改 `obj` 的任何属性.
obj.answer = 43;
++obj.answer;
obj.otherProperty = 'test';
Object.assign(obj, { anotherProperty: 'test2' });

// **不能** 通过赋值覆盖 `obj`. 代码会抛出错误 "assignment to constant variable"
obj = { answer: 41 };
```

## 冻结对象

[Object.freeze()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze)可以“冻结”一个对象。JavaScript阻止向冻结对象进行添加、移除和修改属性。

```javascript
const obj = Object.freeze({ answer: 42 });

// 当 `obj` 被冻结, 修改已有属性或添加新属性会抛出错误：
// "Cannot assign to read only property 'answer' of object '#<Object>'"
obj.answer = 43;
```

然而，`Object.freeze()`**不是**递归的，仍然可以修改嵌套对象的属性。

```javascript
const obj = Object.freeze({ nested: { answer: 42 } });

// **不会** 抛出错误. `obj` 是冻结对象, 但 `nested` 不是.
obj.nested.answer = 43;
```

有很多库可以递归的冻结对象，例如[deep-freeze](https://www.npmjs.com/package/deep-freeze)。

## 严格模式

用`Object.freeze()`时一定要注意：在非[严格模式](https://masteringjs.io/tutorials/fundamentals/strict)，修改`Object.freeze()`冻结的对象**不会**抛出错误。即使在严格模式函数中冻结的对象，在非严格模式修改对象属性也不会抛出错误。

```javascript
function strict() {
  'use strict';
  return Object.freeze({ answer: 42 });
}

function run() {
  const obj = strict();
  // 不会抛出错误，因为该函数是非严格模式
  ++obj.answer;

  obj.answer; // 还是42
}

run();
```