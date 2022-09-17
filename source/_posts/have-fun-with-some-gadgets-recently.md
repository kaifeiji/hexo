---
title: 最近玩的一些小玩意
date: 2022-09-16 14:31:43
categories:
- 折腾
tags:
- NUC
- 小主机
- 微积木
- DIY
- 显示器
- 硬盘
- 存储
---

在我不懈的努力下，我的ThinkPad E530c终于被折腾的黑了，起不来了。

但我还不打算让它安息。

<!-- more -->

## DIY显示器

用微积木拼了一个显示器外壳（超级玛丽），买了块驱动板，用旧笔记本的屏幕做了一块还不错的副屏。

![](/post-images/have-fun-with-some-gadgets-recently-2022-09-16-14-44-16.jpg)

![](/post-images/have-fun-with-some-gadgets-recently-2022-09-16-14-45-28.jpg)

### 驱动板

驱动板，淘宝大概50左右。需要先把旧笔记本屏拆下来，型号标签拍照给客服，客服会告诉你合适型号的驱动板。

### 微积木

微积木不贵，我买了4800颗，才60块，拼完15.6寸的屏幕+底座，还剩1000多颗。

拼起来不难：

1、屏幕宽和高，除以微积木尺寸（8mm），得到横向和纵向的积木数

2、找合适的像素图（不能大于横向和纵向的积木数）

3、比着屏幕拼边框，然后填充背板图案

4、嵌入屏幕，屏幕正面边缘加一圈积木，卡住屏幕（记得先把驱动板的线插上甩出来）

5、比着驱动板拼底座，留好线的口

6、连接驱动板的线，把线整理到底座内部

7、最后把屏幕和底座扣起来，封好口，完工

对了，要提前计算好图案需要的颜色比例。我有一部分没有计算好，黑色没了，用深棕色替代，小遗憾。

## 英特尔6代NUC小主机拱门峡谷

NUC6CAYH准系统，没有内存和硬盘，正好可以把旧笔记本的内存和硬盘用上。

个头小，接口齐全，CPU是J3455，看中的就是它的低功耗。

![](/post-images/have-fun-with-some-gadgets-recently-2022-09-16-14-46-22.jpg)

![](/post-images/have-fun-with-some-gadgets-recently-2022-09-16-14-47-35.jpg)

到货后翻车了，旧笔记本的内存是DDR3，而这个NUC需要DDR3L（低压版）。

只好把DDR3挂闲鱼，又从淘宝买了两条4G的DD3L，一进一出，相当于没花钱。

装上Mame，插上手柄，连上超级玛丽显示器，又完美的继承了旧笔记本的游戏主机大业（见上图）。

## 西数监控紫盘

孩子出生后的监控视频，已经2个多T了，不得已买了新的硬盘，硬件网络折腾一番。

硬盘是西数4T监控紫盘（WD42EJRX），优点：户外监控级别的防护和稳定性，缺点：速度比上一代WD40EJRX略慢（150MB降到140MB）。

很奇怪的是，国外是紫盘比蓝盘、红盘贵，到国内反而是紫盘最便宜。

第一次在淘宝的南京梵多买了两块，打开第一块就翻车了，第二块没开包装，直接退了。他家硬盘货源应该没问题，怀疑是中通的暴力分拣运输搞坏了。

第二次小心翼翼在京东搞了一块，插到硬盘盒，准备开始享受140MB/s的传输速度。

不出所料，又翻车了，4MB/s。

我是从树莓派的硬盘，传到台式机的硬盘：raspi硬盘-raspi网卡-网线-路由器-网线-pc网卡-pc硬盘。一个一个排除吧：

1、raspi硬盘，本地拷贝`rsync --progress /a/big-file.zip /b/`，满速，排除

2、pc硬盘，直接复制粘贴，满速，排除

3、pc直接通过网络向另一台机器传文件，满速，排除pc网卡和路由器的问题

4、raspi网卡，`ethtool eth0`，1000m全双工没问题，排除

5、换了根网线，直接满速！

总结经验：网络问题，先看网线，再看路由器，最后看网卡！

## 总结

在ThinkPad E530C的游戏机退役后，NUC小主机继承衣钵（显示器、硬盘、内存），成功上位。

新增大容量存储，顺便解决了劣质网线导致的问题。

我从来没想过！树莓派连着的标着CAT6的六类网线！一直是一根劣质网线！

难怪用samba放电影的时候会卡！我还一直错怪树莓派的性能不好！