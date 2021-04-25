---
title: '巧用XCOPY，备份和复制文件'
date: 2018-02-11 22:57:27
categories:
- 运维实施
tags:
- 复制文件
- XCOPY
---

![XCOPY](/post-images/qiao-yong-xcopybei-fen-he-fu-zhi-wen-jian.png)

Windows 的复制、粘贴功能非常方便，但在某些场景下并不好用：

1. 复制粘贴单个超大文件或海量小文件时
2. 在不同服务器间用 FTP 或共享文件夹复制文件
3. 需要定时执行备份的场景

在这些场景下，可以使用更可靠的复制粘贴工具——XCOPY。

<!-- more -->

# 一、XCOPY 命令说明

XCOPY 命令基本结构如下：

`XCOPY 来源目录 目标目录 [参数]`

具体参数如下图：

![XCOPY](/post-images/1561820868450.png)

# 二、XCOPY 示例

建立目录 A，目录 A 下有业务 1、业务 2 两个文件夹和一个图片文件：

![XCOPY](/post-images/1561820910347.png)

建立空文件夹目录 B：

![XCOPY](/post-images/1561820893748.png)

打开 cmd，执行如下语句：

![XCOPY](/post-images/1561820917553.png)

命令执行后，目录 B 中建立了和目录 A 相同的目录结构，并复制了目录 A 中的文件：

![XCOPY](/post-images/1561820922669.png)

# 三、定时备份

1、将 XCOPY 命令写为.bat 文件。

2、Win+R，输入 taskschd.msc，打开任务计划程序：

![XCOPY](/post-images/1561820949668.png)

3、创建任务，设定触发器，设置执行时间：

![XCOPY](/post-images/1561820956414.png)

4、在操作标签页，选择步骤 1 中写好的.bat 文件：

![XCOPY](/post-images/1561820963660.png)
