---
title: 'Ubuntu搭建Samba媒体中心'
date: 2020-09-27 18:58:32
categories:
- 折腾
tags:
- Samba
- Ubuntu
- 局域网
---

Samba是Linux和UNIX系统上SMB协议的实现，通过它可以实现局域网内的文件和媒体共享。

<!-- more -->

# 环境

硬件环境：ThinkPad E530c

操作系统：Ubuntu 20.0.4

# 操作步骤

-   安装 Samba

```bash
sudo apt-get install samba samba-common
```

-   创建一个用于分享的 samba 目录

```bash
sudo mkdir /home/{username}/share
```

-   给创建的这个目录设置权限

```bash
sudo chmod 777 /home/{username}/share
```

-   修改 Samba 配置文件

```bash
sudo nano /etc/samba/smb.conf
```

**配置文件中最关键的两句**：

-   server min protocol = NT1（允许 Samba 1.0 版本协议，否则许多 Android 的手机和电视盒子不识别）
-   guest ok = yes（允许匿名访问，反正家庭局域网，没别人能访问）

```ini
[global]
   workgroup = WORKGROUP
   server min protocol = NT1
   server string = ubuntu
   log file = /var/log/samba/log.%m
   max log size = 1000
   netbios name = ubuntu
   security = user
   map to guest = Bad user
[share]
   path = /media/{username}/share
   available = yes
   browseable = yes
   public = yes
   writable = yes
   guest ok = yes
```

-   重启 samba 服务器

```bash
sudo service smbd restart
```

# 使用

Windows 系统：在文件管理器的地址栏，输入\\{ip}\share，即可访问

电视盒子：一般都带有“媒体中心”应用，会主动发现局域网内的 Samba 服务

手机：可以在应用市场搜索 Samba，下载支持 Samba 的媒体播放器，进行视频等资源的浏览、播放
