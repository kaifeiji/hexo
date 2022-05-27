---
title: 如何复制一部分文件，同时创建原来路径的文件夹结构？
date: 2022-02-09 09:53:11
categories:
- 运维
tags:
- 复制
- 文件夹结构
---

之前用verdaccio做内网npm私服时，最初是采用批处理脚本的方式，做局部更新，并保持原有的文件夹结构，复制部分文件到内网。

最近发现了一个非常不错的方法，用everything的高级复制，轻松实现。

<!-- more -->

## 前提

Windows系统

已安装everything

## 步骤

1、打开everything，进入搜索-高级搜索，选择要进行部分复制的文件夹

![](/post-images/partial-copy-with-directory-structure-2022-02-09-11-05-06.png)

2、通过文件名、修改日期等，过滤出要复制的部分文件

3、Ctrl+A全选，或Shift部分选择，编辑-高级-高级复制，修改目标文件夹，确定

![](/post-images/partial-copy-with-directory-structure-2022-02-09-11-08-03.png)

## 结果

过滤后所选中的文件，按照原文件夹的结构，复制到了新的文件夹。

## 原文

作者：祺哥儿
链接：[https://www.zhihu.com/question/263394527/answer/496060119](https://www.zhihu.com/question/263394527/answer/496060119)
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。