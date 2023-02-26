---
title: JavaScript中“use strict”的作用
date: 2023-01-17 23:41:30
categories:
- Mastering JS
tags:
- use strict
---

原文：[What Does "use strict" Do in JavaScript?](https://masteringjs.io/tutorials/fundamentals/strict)

JavaScript的“use strict”会开启严格模式。本文解释了何为严格模式。

<!-- more -->

[JavaScript的严格模式](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode)对于处理常见的错误更为严格。除非有足够的理由，否则最好开启严格模式。

要开启严格模式，在文件或函数以字符串`'use strict'`作为第一个表达式即可。

```javascript
// 如果是一个 .js 文件, 可以在文件的开头写入'use strict'.
// 在 'use strict' 之前，可以写注释
'use strict';
```

```javascript
function foo() {
  'use strict';
  // 即使 'foo' 外的代码不是严格模式，`foo()`函数体内的代码也是严格模式
}
```

```javascript
var x = 1;
// 这里 **不会** 启用严格模式，因为上一条语句是文件的第一个表达式。
'use strict';
```

使用严格模式主要有几个好处：

## 未声明变量的引用错误

在非严格模式，以下代码隐式的创建了一个全局变量x。

```javascript
function foo() {
  x = 42;
}

foo();
x; // 42, `x` 现在是一个全局变量!
```

在严格模式下，在未使用`let`或`var`声明`x`时，设置`x = 42`会抛出错误：

```javascript
'use strict';

function foo() {
  x = 42; // ReferenceError: x is not defined
}

foo();
```

## this默认指向undefined

在非严格模式下，调用一个未绑定任何对象的函数时，`this`会指向全局对象。是不是很违反直觉？

```javascript
const foo = function() {
  console.log(this); // Object [global] { ... }
};

foo();
```

严格模式下，`this`指向`undefined`。

```javascript
'use strict';

const foo = function() {
  console.log(this); // undefined
};

foo();
```

## 强制只读属性

[Object.freeze()函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze)可以使JavaScript对象成为不可变对象，对冻结的对象，不能添加、移除属性，也不能修改已有属性。

但是，`Object.freeze()`有个意想不到的问题：它**只在**严格模式下禁止修改对象，在非严格模式，修改一个冻结的对象，JavaScript运行时会[允许操作](https://www.youtube.com/watch?v=aeHJHjkwDuM)。

```javascript
const frozen = function() {
  'use strict';
  return Object.freeze({ answer: 42 });
};

const unstrict = function() {
  const obj = frozen();
  // 该函数是非严格模式，因此可以修改冻结对象
  obj.answer = 43;
  console.log(obj.answer);
};

const strict = function() {
  'use strict';
  const obj = frozen();
  // TypeError: Cannot assign to read only property 'answer' of object '#<Object>'
  obj.answer = 43;
};

unstrict();
strict();
```

## 不允许对原始类型设置属性

在非严格模式，对数值变量设置属性，会失败但不报错。

```javascript
let answer = 42;

// 非严格模式失败不报错
answer.prop = 'test';

// undefined
console.log(answer.prop);
```

严格模式下，对数值变量设置属性，会抛出`TypeError`错误。

```javascript
'use strict';

let answer = 42;

// TypeError: Cannot create property 'prop' on number '42'
answer.prop = 'test';
```

## 阻止删除prototype

非严格模式下，对不可删除的属性执行delete会失败但不报错：

```javascript
// 非严格模式下失败不报错
delete Object.prototype;

const obj = { answer: 42 };
console.log(obj.constructor.prototype); // {}
```

在严格模式下，删除`prototype`抛出`TypeError`错误：

```javascript
'use strict';

// TypeError: Cannot delete property 'prototype' of function Object() { [native code] }
delete Object.prototype;
```
