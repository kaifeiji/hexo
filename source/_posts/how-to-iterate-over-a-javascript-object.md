---
title: 如何遍历JavaScript的对象
date: 2023-02-23 18:08:30
categories:
- Mastering JS
tags:
- 遍历
---

原文：[How to Iterate Over a JavaScript Object]()

有许多种方法可以遍历JavaScript对象的所有key和value。本文介绍3种方法和它们的优缺点。

<!-- more -->

Suppose you have a simple JavaScript object:

const obj = {
  name: 'Luke Skywalker',
  title: 'Jedi Knight',
  age: 23
};
How do you iterate over the key/value pairs and print out "name: Luke Skywalker", "title: Jedi Knight", and "age: 23"? There are a lot of ways to do this in modern JavaScript. Here's 3 different ways:

Using Object.entries()
The Object.entries() function returns an arrau coontaining the object's key/value pairs. Here's how you would print out the key/value pairs using Object.entries() and a for/of loop.

const obj = {
  name: 'Luke Skywalker',
  title: 'Jedi Knight',
  age: 23
};

// Prints out:
// 'name: Luke Skywalker'
// 'title: Jedi Knight'
// 'age: 23'
for (const [key, value] of Object.entries(obj)) {
  console.log(`${key}: ${value}`);
}
In JavaScript, an entry is an array with exactly 2 elements, where the first element is the key and the second element is the value. The entry pattern shows up in several other places in JavaScript, like the Map constructor and Object.fromEntries().

If you convert the iterator that Object.entries() returns into an array using Array.from(), you'll get an array containing the object's key/value pairs.

const obj = {
  name: 'Luke Skywalker',
  title: 'Jedi Knight',
  age: 23
};

const keyValuePairs = Object.entries(obj);
keyValuePairs[0]; // ['name', 'Luke Skywalker']
keyValuePairs[1]; // ['title', 'Jedi Knight']
keyValuePairs[2]; // ['age', 23]
Using Object.keys()
The Object.keys() function returns an array of the object's keys, as opposed to both the keys and the values. You can then use square brackets [] to get the object's values.

const obj = {
  name: 'Luke Skywalker',
  title: 'Jedi Knight',
  age: 23
};

// Prints out:
// 'name: Luke Skywalker'
// 'title: Jedi Knight'
// 'age: 23'
for (const key of Object.keys(obj)) {
  console.log(`${key}: ${obj[key]}`);
}
Why use Object.keys() versus Object.entries()? Because you can't change the value in an entry to change the value in the object.

const obj = {
  name: 'Luke Skywalker',
  title: 'Jedi Knight',
  age: 23
};

// Assigning to `value` does **not** change the property
// value! You need to do `obj[key] = newValue`
for (let [key, value] of Object.entries(obj)) {
  if (key === 'title') {
    value = 'Jedi Master';
  }
}
obj.title; // 'Jedi Knight'

// Works!
for (const key of Object.keys(obj)) {
  if (key === 'title') {
    obj[key] = 'Jedi Master';
  }
}
With for/in
The Object.keys() and Object.entries() functions only loop over an object's own properties. For a POJO, this distinction doesn't matter. But when you use inheritance, this distinction can be important.

Using a for/in loop lets you iterate over all an object's keys, including inherited keys.

function JediKnight(name, age) {
  this.name = name;
  this.age = age;
}
// `title` is an inherited property for instances of the
// `Jedi Knight` class.
JediKnight.prototype.title = 'Jedi Knight';

const obj = new JediKnight('Luke Skywalker', 23);

// `entries` will **not** include the `title` property
const entries = Object.entries(obj);
entries; // [['name', 'Luke Skywalker'], ['age', '23']]

// Includes `title`, because `for/in` also loops over
// inheritted properties.
// 'name: Luke Skywalker'
// 'age: 23'
// 'title: Jedi Knight'
for (const key in obj) {
  console.log(`${key}: ${obj[key]}`);
}
Generally speaking, you should use Object.keys() or Object.entries() with POJOs to avoid accidentally picking up inheritted properties. But you can use for/in if you're sure you want to loop over inheritted properties.