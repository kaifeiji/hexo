---
title: Puppeteer简介
date: 2023-02-23 17:46:24
categories:
- Mastering JS
tags:
- Puppeteer
---

原文：[Introduction to Puppeteer](https://masteringjs.io/tutorials/fundamentals/puppeteer)

Puppeteer使用真实浏览器测试JavaScript应用，是非常好的工具。

<!-- more -->

[Puppeteer](https://www.npmjs.com/package/puppeteer)是谷歌官方支持的用Node.js控制Chrome的库。你可以从Node.js打开Chrome、跳转到谷歌、搜索点什么、然后查看结果。你也可以用[无头模式](https://github.com/puppeteer/puppeteer#default-runtime-settings)运行Puppeteer，让它在后台运行。

例如，你可以用Puppeteer和Node.js让Chrome加载谷歌的首页：

```javascript
const puppeteer = require('puppeteer');

run().then(() => console.log('Done')).catch(error => console.log(error));

async function run() {
  // 设置`headless: false`可以打开浏览器窗口
  // 这样可以看到实际操作
  const browser = await puppeteer.launch({ headless: false });

  // 打开新页面，跳转到google.com
  const page = await browser.newPage();
  await page.goto('https://google.com');

  // 等5秒钟
  await new Promise(resolve => setTimeout(resolve, 5000));

  // 关闭浏览器，退出脚本
  await browser.close();
}
```

输出结果类似下图：

![Puppeteer操作Chrome](/post-images/introduction-to-puppeteer-2023-02-23-17-47-49.png)

## 执行JavaScript

Puppeteer的页面有一个好用的`evaluate()`函数，可以在Chrome窗口中执行JavaScript。`evaluate()`函数是Puppeteer最灵活的交互方式，因为它允许你使用浏览器API控制Chrome，例如`document.querySelector()`。

以下示例脚本在谷歌搜索了“JavaScript”，获取了前10个结果。

```javascript
const puppeteer = require('puppeteer');

// 后台运行（无头模式）
const browser = await puppeteer.launch({ headless: true });

// 跳转到谷歌
const page = await browser.newPage();
await page.goto('https://google.com');

// 搜索框输入"JavaScript"
await page.evaluate(() => {
  document.querySelector('input[name="q"]').value = 'JavaScript';
});

// 点击搜索按钮，等待页面加载
await Promise.all([
  page.waitForNavigation(),
  page.evaluate(() => {
    document.querySelector('input[value="Google Search"]').click();
  })
]);

// 获取所有搜索结果
const links = await page.evaluate(function getUrls() {
  return Array.from(document.querySelectorAll('a cite').values()).
    map(el => el.innerHTML);
});

await browser.close();
```

## 在本地Web服务器使用Puppeteer

因为Node.js使用事件循环，在同一个脚本启动express服务器并连接到Puppeteer是非常方便的。这意味着很容易[用Puppeteer测试Vue页面](https://thecodebarbarian.com/testing-vue-apps-with-puppeteer-and-mocha)。

```javascript
const express = require('express');
const puppeteer = require('puppeteer');

// 启动express，渲染一个计数器的Vue页面
const app = express();
app.get('/', function(req, res) {
  res.send(`
  <html>
    <body>
      <script src="https://unpkg.com/vue/dist/vue.js"></script>

      <div id="content"></div>

      <script type="text/javascript">      
        const app = new Vue({
          data: () => ({ count: 0 }),
          template: \`
            <div>
              <div id="count">
                Count: {{count}}
              </div>
              <button v-on:click="++count">Increment</button>
            </div>
          \`
        });
        app.$mount('#content');
      </script>
    </body>
  </html>
  `);
});
const server = await app.listen(3000);

// 后台运行（无头模式）
const browser = await puppeteer.launch({ headless: true });
const page = await browser.newPage();
await page.goto('http://localhost:3000');

// 加载当前计数
let count = await page.evaluate(() => {
  return document.querySelector('#count').innerHTML.trim();
});
count; // 'Count: 0'

// count递增，检查计数器是否增长
await page.evaluate(() => {
  document.querySelector('button').click();
});

count = await page.evaluate(() => {
  return document.querySelector('#count').innerHTML.trim();
});
count; // 'Count: 1'

await browser.close();
await server.close();
```