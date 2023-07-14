---
title: 用interact.js实现拖拽、缩放、吸附
date: 2023-07-01 17:15:43
categories:
- JavaScript
tags:
- drag
- drop
- resize
- snap
---

interact.js和大多数拖拽库相比有点不太一样：它提供了简单、灵活的API，对外暴露操作DOM元素所需的全部鼠标或触摸事件信息。但为了更自主的控制，**它不会对DOM元素进行任何操作！**你必须自己操作DOM，实现UI的响应——拖拽、绘制或者滚动页面等。

<!-- more -->

这是一个很特别的拖拽库：

* 将不同浏览器和设备中的原生鼠标或触摸事件，封装成自定义事件，以一致的API将用户操作传递给事件回调
* 不会对DOM进行实际的拖拽操作，需要在事件回调中，自行实现拖拽或其他任何DOM操作
* 提供modifier接口，可以对自定义事件进行修改，以实现吸附、限制等功能

## 示例

为了避免枯燥的概念，可以先看一个用interact.js实现的，包含吸附和左对齐的组件拖拽demo：

[组件拖拽demo](/post-attach/drag-and-drop.html)

## Action（操作）

Action是interact.js定义的一种概念，有三种Action——Draggable、Resizable和Gesturable，对应拖拽、缩放、手势（移动端）三种操作。

在这些Action中，实现了对鼠标或触摸操作中原生事件的封装，并将事件对象interaction暴露给事件回调。

### Action基本语法

Draggable、Resizable和Gesturable三者的基本语法是一致的，以Draggable为例：

```html
<style>
.draggable {
  width: 200px;
  height: 100px;
  touch-action: none;
  user-select: none;
}
</style>
<script src="https://unpkg.com/interactjs/dist/interact.min.js"></script>
<div class="draggable"> Draggable Element </div>
<script>
const position = { x: 0, y: 0 }
// 用CSS选择器构造Interactable对象，也可以直接传入DOM元素
interact('.draggable')
// Interactable对象定义拖拽操作
.draggable({
  // 开始拖拽回调，触发一次
  onstart (event) {
    console.log(event.type, event.target)
  },
  // 拖拽移动回调，触发多次
  onmove (event) {
    position.x += event.dx
    position.y += event.dy
    // 进行实际的DOM操作，实现拖拽的UI响应
    event.target.style.transform = `translate(${position.x}px, ${position.y}px)`
  },
  // 结束拖拽回调，触发一次
  onend (event) {
    console.log(event.type, event.target)
  },
})
</script>
```

### Action的三种回调形式

需要注意的是，Action的回调函数提供了三个语法形式，它们的效果完全一样，可以选择任意一个使用。

```javascript
// 直接在Action参数中定义
interact('.draggable').draggable({
  onstart (e) { },
  onmove (e) { },
  oninertiastart (e) { },
  onend (e) { },
})
// 在Action参数的listeners中定义
interact('.draggable').draggable({
  listeners: {
    start (e) { },
    move (e) { },
    inertiastart(e) { },
    end (e) { },
  },
})
// 用Interactable的on方法定义
interact('.draggable').draggable({})
.on('dragstart', (e) => {})
.on('dragmove', (e) => {})
.on('draginertiastart', (e) => {})
.on('dragend', (e) => {})
```

Resizable对应的三种形式的事件为：

* 直接定义 - `onstart` `onmove` `oninertiastart` `onend`
* listeners定义 - `start` `move` `inertiastart` `end`
* on定义 - `resizestart` `resizemove` `resizeinertiastart` `resizeend`

Gesturable对应的三种形式的事件为：

* 直接定义 - `onstart` `onmove` `onend`
* listeners定义 - `start` `move` `end`
* on定义 - `gesturestart` `gesturemove` `gestureend`

### Action参数

Draggable、Resizable、Gesturable三种Action，有一些公共参数([Action Options](https://interactjs.io/docs/action-options/))，公共参数可以对Action进行一些扩展或约束：


* `max` - 限制Interactable的事件绑定数
* `maxPerElement` - 限制Interactable对象和对应元素的事件绑定数
* `manualStart` - 手动启动事件
* `hold` - 长按指定毫秒数，才能触发Action
* `inertia` - 实现惯性拖拽、缩放
* `styleCursor` - 开关拖拽、缩放的鼠标指针样式
* `cursorChecker` - 根据事件手动设置鼠标指针样式
* `autoScroll` - 鼠标超出范围时，是否允许容器滚动
* `allowFrom` - 定义可操作的范围或元素（相当于拖拽、缩放把手）
* `ignoreFrom` - 定义不可操作的范围或元素
* `enabled` - 是否启用

除了这些公共参数，Draggable和Resizable还分别有各自的专有参数，可以在文档各章节找到：

* [Draggable](https://interactjs.io/docs/draggable/#lockaxis-and-startaxis)
* [Resizable](https://interactjs.io/docs/resizable/#invert)

### Action的启用/关闭状态

用`enabled`参数，可以设置Action的开关状态。

```javascript
// 初始化一个不启用的Draggable
interact('.draggable').draggable({
  enabled: false,
  onmove (event) { console.log(event) },
})
// 在合适的时机再启用
interact('.draggable').draggable({
  enabled: true,
})
```

### Dropzone（放置区）

Dropzone可以定义一个拖拽的放置区，主要和Draggable配合，实现拖拽-移动-放置的效果。

在Dropzone的回调事件中，可以操作放置区本身（`event.target`），也可以操作拖拽元素（`event.relatedTarget`）。

```javascript
interact('.dropzone').dropzone({
  // 只接受.draggable的拖放
  accept: '.draggable',
  // 判断何时可以松开鼠标，实现放置
  overlap: 1.0,
  // 也有三种形式的事件回调
  ondropactivate: (event) => { },
  ondropmove: (event) => { },
  ondragenter: (event) => { },
  ondragleave: (event) => { },
  ondrop: (event) => { },
  ondropdeactivate: (event) => { },
})
```

## Modifier（修改器）

在Action中，`modifiers`参数接收一个数组，数组包含一系列的Modifier对象，用于按顺序对Action的事件进行修改。

interact.js提供了两种内置的Modifier——Restrict（限制）和Snapping（吸附）。

### Restrict（限制）

Restrict是一种限制鼠标或元素移动范围的Modifier。

如以下示例代码，`interact.modifiers.restrictRect`定义了拖拽事件中，元素不能超出父元素的修改器。

```javascript
interact('.son').draggable({
  modifiers: [
    interact.modifiers.restrictRect({
      restriction: 'parent',
    })
  ],
  onmove(event) { },
})
```

它会在onmove事件回调触发前，判断元素是否超出了父元素：

* 如果x方向超出父元素，将`event.dx`（x方向位移）修改为0
* 如果y方向超出父元素，将`event.dy`（y方向位移）修改为0
* 如果没有超出父元素，不进行修改，忠实反映用户鼠标移动

这样，在onmove中定义的DOM操作，会在元素即将超出父元素时停止移动，实现 `restrictRect` 的限制逻辑。

除了restrict，还可以根据不同的需要，使用`restrict`、`elementRect`、`restrictSize`、`restrictEdge`（[Restrict](https://interactjs.io/docs/restriction/#restrict)）。

### Snapping（吸附）

Snapping也是一种Modifier，它指定一系列的坐标点或轴线，在元素靠近时，实现吸附效果。

如以下示例代码，`interact.modifiers.snap`定义了拖拽事件中，元素的左上角、中心和右下角，在靠近到50px距离时，会吸附到父元素(300, 300)的坐标点。

```javascript
interact('.draggable').draggable({
  modifiers: [
    interact.modifiers.snap({
      offset: 'parent',
      targets: [ { x: 300, y: 300 } ],
      range: 50,
      relativePoints: [
        { x: 0, y: 0 },
        { x: 0.5, y: 0.5 },
        { x: 1, y: 1 }
      ]
    })
  ],
  onmove (event) { }
})
```

它会在onmove事件回调触发前，判断元素的3个点和(300, 300)的距离：

* 如果距离减小到50px，修改`event.dx`，`event.dy`，吸附过去
* 如果距离增大超过50px，修改`event.dx`，`event.dy`，取消吸附，有一个弹开的效果
* 如果距离大于50px，不进行修改，忠实反映用户鼠标移动

这样，在onmove中定义的DOM操作，会实现真正的吸附效果。

除了snap，还可以根据不同的需要，使用`snapSize`、`snapEdges`、[Snapping]（(https://interactjs.io/docs/snapping/#snapsize)）。

### Snapping的targets

Snapping中的 `targets` 参数，接收一个数组，数组中可以是：

* 类似 `{ x: 100, y: 100 }` 的吸附点对象
* 类似 `{ x: 100 }` 的吸附线对象
* 一个函数，接收当前事件信息，返回吸附点或吸附线对象
* 用 `interact.snappers.grid` 生成的网格

数组中的多个对象或函数，interact.js会在内部判断距离最近的吸附，最终执行吸附。

其中直接传入点对象、线对象和网格比较死板，不能实现动态的吸附效果。

用函数的方式，可以根据事件信息、元素相对位置等，通过实时计算，达到动态吸附效果：

```javascript
function snapFunc (
  x,
  y,
  interaction, 
  { x: offsetX, y: offsetY, relativePoint, index: relativePointIndex },
  index
) {
  let snapX = 0, snapY = 0, snapRange = 100
  // 根据实际需要，计算吸附的坐标值和吸附距离
  return {
    x: snapX,
    y: snapY,
    range: snapRange,
  }
},
interact('.draggable').draggable({
  modifiers: [
    interact.modifiers.snap({
      offset: 'parent',
      targets: [ snapFunc ],
      range: 50,
      relativePoints: [
        { x: 0, y: 0 },
        { x: 0, y: 1 },
        { x: 1, y: 1 },
        { x: 1, y: 0 },
      ]
    })
  ],
  onmove (event) { }
})
```

## Pointer事件

除了Action这种组合操作的事件，interact.js也支持原子化的鼠标或触摸事件：

* `down`
* `move`
* `up`
* `cancel`
* `tap`
* `doubletap`
* `hold`

例如长按事件：

```javascript
interact('.button').on('hold', (event) => {
  console.log(event.type, event.target)
})
```

## 总结

interact.js的使用全流程：

* interact.js通过`interact('.css-selector')`初始化一个Interactable对象
* Interactable对象可以定义Action，包括Draggable、Resizable和Gesturable，也可以定义一个Dropzone放置区
* Action在鼠标或触摸操作过程中，将原生事件封装为interaction事件，并暴露给Action的事件回调
* 使用者可以在Action的事件回调中，根据interaction事件反映的用户操作，进行实际的DOM操作
* DOM操作的自由度，可以非常灵活的实现各种UI响应，局限只在于你的想象力
* Modifier提供了一种修改interaction事件的机制，官方默认提供了Snapping（吸附）和Restrict（限制）两种修改器
* 可以参照Modifier源码，创造一个鸭子类型的修改器，修改interaction事件，实现自定义的效果

总体来说，interact.js是一个比较简单的拖拽库，其优势在于灵活性——你可以让拖拽行为变成在canvas上画画，也可以用手势实现一个类似愤怒小鸟的拉弹弓的弹性效果。另外，如果对于性能有非常极限的要求，你可以在DOM操作中做各种艺术体操，满足性能指标。

如果你只是简单想实现一个拖拽商品列表的功能，那么建议不要使用interact.js，去用其他正常一点的拖拽库吧。