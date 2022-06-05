---
title: 内网搭建npm私服——Verdaccio
date: 2021-05-08 17:16:08
categories:
- 运维
tags:
- Verdaccio
- npm
- 私服
---

最近在公司搭建Vue-CLI开发环境，因为内外网物理隔离，遇到最大的障碍就是npm。

经过几种方案的尝试，最终选择了Verdaccio搭建内网的npm私服。

<!-- more -->

# Verdaccio

> A lightweight open source private npm proxy registry

官网的介绍名副其实，确实非常轻量级。

其实Verdaccio的初衷是发布私有包，不过因为它的缓存机制很棒，用于内网npm私服非常方便。

## 外网搭建Verdaccio

首先要在外网部署好node环境，搭建Verdaccio服务。

外网的Verdaccio作用主要是缓存所需要的npm包，毕竟没有镜像整个npm的实力:D

### 一键安装

```bash
npm install -g verdaccio
```

### 配置Verdaccio

配置文件位置(user要替换成Windows用户名，~代表Linux当前用户)：

Windows：C:\Users\user\AppData\Roaming\verdaccio\config.yaml
Linux：~/.config/verdaccio/config.yaml

将npmjs的url替换为淘宝源（国内）

```yaml
# a list of other known repositories we can talk to
uplinks:
  npmjs:
    url: https://registry.npm.taobao.org
```

### 一键启动

```bash
verdaccio
```

默认的访问地址是`http://localhost:4873/`

### 修改npm源

这里需要将Verdaccio设为npm的registry：

```bash
npm set registry http://localhost:4873/
```

设置后，再执行npm时的路径：

npm install -> Verdaccio -> 淘宝源

Verdaccio会根据本地缓存情况，自动从淘宝源下载未缓存的包，提供给用户使用。

## 缓存npm包

### 方案1：空项目

建一个空的npm项目：

```bash
npm init
```

name写test，其他一路回车即可，最终获得一个只有`package.json`的空项目。

直接`npm install a b c d ...`，或者

向package.json的依赖加入需要的包，然后`npm install`

```json
{
  "name": "test",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "axios": "^0.21.1",
    ...
  },
  "devDependencies": {
    "eslint": "^7.12.1",
    ...
  }
}
```

### 方案2：项目模板

可以采用各类开源的项目模板，例如vue-element-admin-template：

```bash
git clone https://github.com/mokeyjay/vue-element-admin-template.git

cd vue-element-admin-template

npm install
```

### 缓存结果

缓存的npm包在以下位置(user要替换成你的用户名)：

Windows：C:\Users\user\AppData\Roaming\verdaccio\storage\
Linux：~/.config/verdaccio/storage/

可以看到以各个包命名的文件夹，文件夹内包含`.tgz`文件和`package.json`文件。

其中`.tgz`文件，视项目需要可以缓存多个版本，例如：

* eslint-5.16.0.tgz
* eslint-6.8.0.tgz
* eslint-7.25.0.tgz

## 内网移植Verdaccio

内网没有npm，是无法安装Verdaccio的，只能移植。

内网部署node环境，将外网机器的npm、verdaccio文件夹打包拷入内网，并放到相同的位置。

Windows：
C:\Users\user\AppData\Roaming\npm\
C:\Users\user\AppData\Roaming\verdaccio\

Linux：
~/.npm/
~/.config/verdaccio/

如果一切正常，可以在内网运行Verdaccio。

```bash
verdaccio
```

同样，将Verdaccio设为内网npm的registry：

```bash
npm set registry http://localhost:4873/
```

如果内网其他同事也要用，把localhost换成机器的IP。

开开心心的`npm install`吧！

# 其他问题

## Verdaccio设为服务

npm源在设为Verdaccio后，每次`npm install`都需要打开Verdaccio。

可以参考官网的解决方案：

[作为 Windows 服务安装](https://verdaccio.org/docs/zh-CN/windows)

## 持续、增量更新

在第一次移植完成后，还需要持续的更新npm私服。

有新的需求，就在外网的test项目安装对应的包，Verdaccio的storage文件夹缓存也会同步更新。

随着缓存的包越来越多，每次从外网向内网全量更新是不现实的。

可以根据storage目录下各个包文件夹的修改时间，筛选出本次更新的包，增量拷入内网。

> 2022-02-09更新
> 参考以下文章，可以更方便的实现增量更新
> [如何复制一部分文件，同时创建原来路径的文件夹结构？](/post/partial-copy-with-directory-structure/)

## 失败的尝试

总结如下：

* 外网建项目，node_modules文件夹整体拷入(压缩、解压慢到死)
* 内网Maven，外网node-tgz-downloader下载tarball(惭愧，玩不转Java的东西)
* cnpmjs.org(太复杂，放弃)
* sinopia(作者已弃坑)
