---
title: Vue的组件间通信方法及示例
date: 2021-06-18 07:09:27
categories:
- 前端
tags:
- Vue
- 组件
- 通信
- 父子
- 传值
---

最近用Vue+Element做一个后台系统，在组件通信方面遇到很多问题。

其实官方文档中关于组件通信，其实已经讲的很详尽了，但内容比较分散，在此总结、复习一下。

<!-- more -->

## 单文件组件（SFC）

本文内容基于Vue的单文件组件，也就是.vue文件，官方文档：

[单文件组件](https://cn.vuejs.org/v2/guide/single-file-components.html)

## 组件概念

组件化开发是Vue的核心思想之一，也是Vue官方最推荐的开发范式。

所谓组件化开发，就是把页面中，具有一定独立性的各个区块或功能，拆分成单个组件进行编码维护。

![组件化开发](https://cn.vuejs.org/images/components.png)

## 收益和成本

组件化开发的收益，两方面：

1. 组件复用：封装一次，到处使用
2. 可维护性：单个组件逻辑简单，方便排错、更新

而成本方面，主要就是组件间的通信，也就是本文的主题。

## 组件级别

从项目结构来看，组件分为三级：

* 页面级组件：最终面向用户展示的单个页面
* 模块级组件：页面中具有特定功能的某个模块，例如：数据列表、工具栏...
* 控件级组件：模块进一步拆分的组件，例如：表格、输入框、按钮...

不同级别的组件，具有各自的功能和特性，高一级的组件包含低一级的组件，呈树形结构：

``` js
page: {
  module1: {
    controlA,
    controlB,
  },
  module2: {
    controlC,
    controlD,
  },
}
```

## 父子组件通信

### 父attribute -> 子prop

father通过title属性，向son组件传值

``` html father.vue
<template>
  <son title="儿子你好"></son>
</template>

<script>
import Son from './son';

export default {
  name: 'Father',
  components: { Son },
}
</script>
```

子接收title并进行展示

``` html son.vue
<template>
  <h3> {{ title }} </h3>
</template>

<script>
export default {
  name: 'Son',
  props: {
    title: {
      type: String,
      default: ()=> '一级标题', // 默认值
      validator(val) {
        return val.length < 100; // 校验函数，长度大于100的不接收
      },
    }
  },
}
</script>
```

**注意**：

1. 父的attribute，是`kebaba-case`，短横线分隔，例如big-title
2. 子的prop，是`camelCase`，除了第一个单词，其他单词首字母大写，例如bigTitle
3. `title="儿子你好"`，这种方式只能传递字符串
4. 传值是数字、变量或表达式，必须用`v-bind:title`或`:title`
5. 子可以对prop设置类型、默认值和校验函数，提高程序健壮性

### 子$emit('event',value)->父@event

子通过$emit事件('hungry')，向父传递值('水果')

``` html son.vue
<template>
  <h3 @click="play"> 我是儿子 </h3>
</template>

<script>
export default {
  name: 'Son',
  methods: {
    play() {
      console.log('玩球');
      console.log('看书');
      console.log('学走路');
      this.$emit('hungry', '水果');
    },
  },
}
</script>
```

父接收hungry事件，并执行喂食方法

``` html father.vue
<template>
  <son @hungry="feed"></son>
</template>

<script>
import Son from './son';

export default {
  name: 'Father',
  components: { Son },
  methods: {
    feed(food) {
      console.log('喂食'+food);
    },
  },
}
</script>
```

**注意**：

1. event的名称，是`kebaba-case`，短横线分隔，例如very-hungry
2. 子$emit('event', value)，value可以是字面量、变量、表达式等
3. 父@event="handler"，handler为事件处理方法，接收子传递的value参数

### 父v-model <-> 子value

父通过v-model，向子传递money，实现双向绑定

父可以通过buy花钱，通过work赚钱，都是对this.money直接操作

父通过v-model把money双向绑定到子，父子共享一个钱包

``` html father.vue
<template>
  <div>
    <h1>我是爸爸</h1>
    <h3 v-for="(price,item) in store" @click="buy(item)">Buy {{ item }} (${{price}}) </h3>
    <h3 @click="work">Work work!</h3>
    <son v-model="money" title="儿子你好"></son>
  </div>
</template>

<script>
import Son from './son';

export default {
  name: 'Father',
  components: { Son },
  data() {
    return {
      store: {
        car: 40,
        house: 50,
      },
      personel: [],
    },
  }
  methods: {
    buy(item) {
      if (this.money > this.store[item]) {
        this.money = this.money - this.store[item];
        personal.push(item);
      } else {
        console.log('破产啦');
      }
    },
    work() {
      this.money += 1;
    },
  },
}
</script>
```

子通过value属性，接收了父的money

可以通过buy花钱，但不能直接操作money，而是通过`this.$('input', newVal)`来更新父的money

``` html son.vue
<template>
  <div>
    <h1> 我是儿子 </h1>
    <h3 v-for="(price,item) in store" @click="buy(item)">Buy {{ item }} (${{price}}) </h3>
  </div>
</template>

<script>
export default {
  name: 'Son',
  props: {
    value: {
      type: Number,
      default: ()=> 0,
      validator(val) {
        return val >= 0;
      },
    }
  },
  data() {
    return {
      store: {
        snack: 5,
        book: 10,
        game: 30,
      },
      personel: [],
    },
  }
  methods: {
    buy(item) {
      if (this.value > this.store[item]) {
        this.$('input', this.value - this.store[item]);
        personal.push(item);
      } else {
        console.log('破产啦');
      }
    },
  },
}
</script>
```

**总结**：v-model本质上，是prop-$emit的语法糖

`v-model="money"`相当于`:value="money" @input="money=$event.target.value"`

这也是，子组件必须使用value和input的原因

*tips*：上文的例子中，可以通过model选项，把默认的属性名value和事件名input，转为更业务化的money和spend

``` js
model: {
  prop: 'money',
  event: 'spend',
},
props: {
  money: ...
},
methods: {
  buy(...) {
    this.$emit('spend',...);
  }
},
```

### 父attribute.sync <-> 子$emit('update:attribute', value)

.sync双向绑定方式，与v-model非常类似

父组件中，只是将v-model改为:money.sync

``` html father.vue
<template>
  <div>
    <h1>我是爸爸</h1>
    <h3 v-for="(price,item) in store" @click="buy(item)">Buy {{ item }} (${{price}}) </h3>
    <h3 @click="work">Work work!</h3>
    <son :money.sync="money" title="儿子你好"></son>
  </div>
</template>

<script>
import Son from './son';

export default {
  name: 'Father',
  components: { Son },
  data() {
    return {
      store: {
        car: 40,
        house: 50,
      },
      personel: [],
    },
  }
  methods: {
    buy(item) {
      if (this.money > this.store[item]) {
        this.money = this.money - this.store[item];
        personal.push(item);
      } else {
        console.log('破产啦');
      }
    },
    work() {
      this.money += 1;
    },
  },
}
</script>
```

子组件将model选项去掉，因为已经不是v-model了

prop部分不变，仍是接收money属性

更新money部分，不再是`this.$emit('input',newVal)`，而是`this.$emit('update:money',newVal)`

``` html son.vue
<template>
  <div>
    <h1> 我是儿子 </h1>
    <h3 v-for="(price,item) in store" @click="buy(item)">Buy {{ item }} (${{price}}) </h3>
  </div>
</template>

<script>
export default {
  name: 'Son',
  props: {
    money: {
      type: Number,
      default: ()=> 0,
      validator(val) {
        return val >= 0;
      },
    }
  },
  data() {
    return {
      store: {
        snack: 5,
        book: 10,
        game: 30,
      },
      personel: [],
    },
  }
  methods: {
    buy(item) {
      if (this.money > this.store[item]) {
        this.$('update:money', this.money - this.store[item]);
        personal.push(item);
      } else {
        console.log('破产啦');
      }
    },
  },
}
</script>
```

**总结**：.sync与v-model一样，也是一种语法糖，不同之处在于，.sync可以绑定多个属性：

``` html
<son :money.sync="money" :house.sync="house" :car.sync="car"></son>
```

### 子`<slot :attr="value">` -> 父`<template #sonSlot="attr">`

子组件通过slot将score传递给父组件

``` html son.vue
<template>
  <div>
    <h3> 我是儿子 </h3>
    <slot :score="score" />
  </div>
</template>

<script>
export default {
  name: 'Son',
  data() {
    return {
      score: {
        math: 100,
        chinese: 50,
        english: 66,
      },
    },
  }
}
</script>
```

父组件通过v-slot接收score，并在子组件内部以插槽形式渲染

``` html father.vue
<template>
  <div>
    <h1>我是爸爸</h1>
    <son>
      <template v-slot="score">
        数学 : {{ score.math }}
        语文 : {{ score.chinese }}
        英语 : {{ score.english }}
      </template>
    </son>
  </div>
</template>

<script>
import Son from './son';

export default {
  name: 'Father',
  components: { Son },
}
</script>
```

### 其他奇技淫巧

父子组件通信，其实还有:

* 父this.$refs.son.sonData/sonMethod，直接操作子组件数据，或调用子组件方法，将值作为参数传给子组件

* 子this.$parent.fatherData/fatherMethod，直接操作父组件数据，或调用父组件方法，将值作为参数传给父组件

这两种方法，都破坏了Vue的`单向数据流`，是官方不推荐的，只在某些极限情况下会用到

## 兄弟组件通信、爷孙组件通信

跨层级或平级组件之间的通信，是很少见的

一般都会通过中间组件，进行数据的中转，而非直接通信

直接通信可以采用$bus总线、Vuex等，本文不再赘述

## 总结

|方法|信息流向|绑定方式|
|-|-|-|
|prop|父 -> 子|单向|
|$emit|子 -> 父|单向|
|v-model|父 <-> 子|双向 单值|
|.sync|父 <-> 子|双向 多值|
|slot|子 -> 父|单向|
|$refs|父 -> 子|单向|
|$parent|子 -> 父|单向|

单向绑定: 适用于纯展示、非交互的组件，例如：统计图、列表...

双向绑定: 适用于数据交互的组件，例如：文本框、下拉框...

具体用哪一种，我也说不太清楚，凭经验吧(((逃
