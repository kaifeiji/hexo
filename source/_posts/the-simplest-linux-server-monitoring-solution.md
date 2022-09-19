---
title: 史上最简单的Linux服务器监控方案
date: 2022-09-19 12:54:48
categories:
- 运维
tags:
- Bark
- Linux
- cron
- 服务器监控
- 推送
---

用Bark+cron，实现了史上最简单的Linux服务器监控方案。

<!-- more -->

市面上的服务器监控工具很多，常见的zabbix、Prometheus等。

这些工具有个共同的缺点：需要安装，而且占资源。

还有不用安装、不占资源的服务器监控方案？那不就是Bash脚本吗！

## 安装Bark

首先，你要有一台iPhone，然后，安装这个红色的APP：

![](/post-images/the-simplest-linux-server-monitoring-solution-2022-09-19-13-02-16.png)

打开Bark，切换到服务器界面，记录带有设备唯一码的URL：

![](/post-images/the-simplest-linux-server-monitoring-solution-2022-09-19-13-04-37.png)

## Bash脚本

进入Linux服务器，编写Bash脚本`/var/scripts/df2bark.sh`。

这里监控的是某个硬盘的容量，如果你需要监控其他参数，请自行编写Bash脚本┑(￣Д ￣)┍。

用`df`命令结合`awk`，获取硬盘已使用百分比`percent`；

如果`percent`大于89，用curl调用Bark的URL；

URL中：`api.day.app`是Bark官方提供的服务，可以替换成自建服务，一串x是你的设备唯一码，后边的是标题和内容。

``` bash
#!/bin/bash
percent=`df -hl | grep /share | awk '{print $5}'`
if ((${percent%%%}>89)); then
  curl https://api.day.app/xxxxxxxxxxxxxxxxx/磁盘容量告警/raspi的share磁盘剩余空间已不足10%25
fi
```

> 注意：URL中的特殊字符要使用encodeURIComponent进行转码，否则Bark服务会报错，例如百分号应为%25，空格应为%20。

## 将Bash脚本加入cron

在Linux执行`crontab -e`，编辑cron设置，加入以下内容保存：

```
30 9 * * * /bin/bash /var/scripts/df2bark.sh
```

cron会在每天早上9点半执行脚本，如果硬盘使用量超过90%，你会接收到通知。

## 总结

类似这种方案适合有Bash脚本基础，又关注资源占用和稳定性的用户。

Bark的监控通知，只对iPhone友好。

安卓用户可以用Push Plus或者Server酱，向微信推送消息，配置比Bark略麻烦一些（需要微信扫码登录）。