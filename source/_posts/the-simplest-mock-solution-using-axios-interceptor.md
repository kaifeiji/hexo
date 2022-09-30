---
title: 史上最简单mock方案——用axios的拦截器
date: 2022-09-30 13:25:23
categories:
- 前端
tags:
- mock
- 假数据
- axios
- 拦截
---

mock：通过模拟，返回约定格式的假数据。

好处：不用等待后端接口开发完成，前端直接开干，提升效率。

<!-- more -->

## mock方案

mock有很多方案，大概分为四类：

1、在代码写死，变量直接赋予一个JSON

缺点是没有请求接口的过程，容易导致一些异步问题，而且“不优雅”。

2、接口管理工具，例如：`swagger`、`moco`、`apifox`等

通过工具生成一个接口，可以定义接口的行为，比较灵活，但也比较繁琐。

3、node服务器，例如：`json-server`、`jsonplaceholder`

扔一个JSON给node服务器，一个JSON就是一个接口。

4、使用拦截器，例如：`mock.js`

本文方案与`mock.js`相似，使用了拦截器的思路，但`mock.js`更优雅、更强大。

虽然它的配置也不复杂，但我就是懒。

## 利弊分析

代码写死，无疑是有问题的。

在多个请求先后执行的页面中，采用变量赋予JSON的方式进行mock，会导致一些异步问题被隐藏起来；

等换成真正的接口，问题暴露出来，再修改难度就很大了。

接口管理工具和node服务器，其实是相同的性质，都是在本地启动了一个服务，生成了实实在在的接口，可供前端调用。

首先就是麻烦，要安装工具、部署服务器；

其次是跟代码分离，不好管理，接口多了就乱了。

## 史上最简单mock方案

原理：使用axios的拦截器，在response拦截器直接返回mock数据。

在`@/utils/axios.js`中定义拦截器如下：

``` javascript
import axios from 'axios';

axios.interceptors.response.use(
  (response) => {
    if (response.config.mock && response.status === 404) {
      return response.config.mock;
    } else {
      return response.data;
    }
  }
);

export default axios;
```

在业务代码中使用mock：

接口还未完成时，请求`/get/user/list`的`response.status`是404，此时返回请求中的mock数据；

接口开发完成后，请求返回的`response.status`不是404，返回的是接口的真实数据。

``` javascript
import axios from '@/utils/axios.js';

axios.request({
  url: '/get/user/list',
  method: 'get',
  mock: [
    {name: '张三', num: 1},
    {name: '李四', num: 2},
    {name: '王五', num: 3},
  ],
}).then((resp) => {
  console.log(resp);
}).catch((err) => {
  console.log(err);
});
```

## 优点

1、史上最简单，无需配置、无需部署

2、真实的走了一遍网络请求，而且返回了预期的数据

3、与代码深度整合（笑），无需额外的组织逻辑

4、url是真实接口路径，接口开发完成后，自动切换到真实数据

5、mock内容可以看作是注释的一部分，即接口返回示例

## 缺点

不优雅