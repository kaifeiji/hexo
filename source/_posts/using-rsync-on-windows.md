---
title: 在Windows中使用rsync
date: 2022-09-18 21:56:39
categories:
tags:
---

本博客部署在VPS上，之前一直用SFTP上传到服务器，不是很方便。

想到用rsync可以远程同步。

<!-- more -->

## 远程同步文件的选项

向Linux服务器远程同步文件，选项还是挺多的：

1、SFTP

Linux发行版一般都自带sftp-server，所以只需要本地装个FileZilla，用SSH账号连接上传即可。

有三个痛点：一是难以用脚本实现自动化，二是加密传输速度慢，三是更新同步不方便（全量覆盖？选择覆盖？）。

2、FTP

Linux需要安装一个FTP服务器，倒也不繁琐；本地还是FileZilla。

解决了SFTP速度慢的问题，但仍然无法实现自动化的更新同步。

3、Git

需要有一个Git服务器做中转，GitHub、GitLab、Gitee，或者自建Git Server也行。

本地和VPS克隆同一个仓库。

本地提交（`git commit -m "new article"`），推送（`git push`）。

VPS拉取（`git pull`）。

实现了增量同步，不用全量覆盖（几百MB）等半天。

4、rsync

Linux自带，速度快，增量同步——简直完美。

## 在Windows中使用rsync

重点来了，怎么在Windows使用Linux的rsync命令呢？

### cwrsync

cwrsync是itefix.net开发的rsync在Windows的实现。

早期有免费版本，目前官网开始收费，不提供免费下载链接，但在其他地方仍然能下载到。

下载解压到`C:\app\cwrsync`，进入cmd：

```
C:\app\cwrsync> cd bin
C:\app\cwrsync\bin> rsync -av --progress /cygdrive/c/some/path/ user@123.123.123.123:/var/www/blog
```

注意：rsync不支持Windows格式的路径（例如C:\some\path\），cwrsync使用/cygdrive/c/some/path/，映射到C盘的对应路径。

### cygwin

cygwin是在Windows上兼容POSIX的模拟层：提供Cygwin Terminal，可以执行Shell命令，例如ls、mkdir等；提供了包管理服务，可以安装包括rsync在内的常见Linux软件。

在安装cygwin的过程中，可以选择安装rsync：

![](/post-images/using-rsync-on-windows-2022-09-18-23-09-32.png)

如果已经安装过cygwin，可以再次执行cygwin的setup安装包，选择安装rsync。

安装完成后，直接使用rsync即可：

![](/post-images/using-rsync-on-windows-2022-09-18-23-12-06.png)

没错，cygwin用的路径也是`/cygdrive/c/some/path/`，因为cwrsync其实是基于cygwin开发的。

### git bash

优点是只要在Windows安装了git就能使用，缺点是安装rsync比较麻烦。

具体参考这位老哥的文章：

[How to use rsync on Git Bash](https://shchae7.medium.com/how-to-use-rsync-on-git-bash-6c6bba6a03ca)

与cygwin不同的是，git bash是基于MinGW在Windows实现的bash shell，它所映射的Windows路径是这样的：`/c/some/path/`。

### WSL

WSL是更重的解决方案，但是一劳永逸。不再赘述。