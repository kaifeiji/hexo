---
title: 'Mame——找回童年记录'
date: 2020-10-20 08:45:42
categories:
- 游戏
tags:
- 旧的回忆
- Mame
- 街机
- 恐龙快打
- 三国志
- 超级马里奥
---

在Ubuntu 20.04 LTS上安装Mame，玩老学校的街机游戏。

<!-- more -->

![恐龙快打](/post-images/mame-zhao-hui-tong-nian-ji-lu.png)

# 环境

硬件：ThinkPad E530c + SFC/SNES 手柄

操作系统：Ubuntu Desktop 20.0.4（Mame 也支持 MacOS 和 Windows）

# 安装步骤

安装 Mame 模拟器

```bash
sudo apt install mame
```

下载游戏 ROM，例如：恐龙快打-dinopic.7z、三国志-wof.7z、超级马里奥-suprmrio.7z

    https://archive.org/details/mame225_rom

将 ROM 放到目录（{user}替换为当前登录用户名）

    /home/{user}/mame/roms

# 启动游戏

运行 Mame 用图形界面选择游戏，或执行命令（{rom}替换为 ROM 的文件名，例如 dinopic）

```bash
mame {rom}
```

# 按键设置

-   按 Tab 键打开设置界面
-   进入 Input(general)
-   进入 User Interface
    -   选择 Pause，设置暂停按键
    -   选择 Save Status，设置存档按键
    -   选择 Load Status，设置读档按键
-   返回上一级，进入 Player 1 Contro
    -   选择 UP DOWN LEFT RIGHT，设置上下左右按键
    -   选择 Button 1 2 3 4，设置 ABXY 按键
    -   设置其他按键
-   返回上一级，设置其他 Player 的按键

# 说明

上文提供的 ROM 下载链接需要科学上网

可以用搜索引擎搜索“mame rom”，从其他途径下载 ROM

NEOGEO 的游戏，必须同时下载 neogeo.7z 才能玩，例如合金弹头（mslug）系列

ROM 文件名与游戏名称的对应关系，可以查看列表：

[琵琶行论坛-CLARKH-Mame游戏中文列表（更新至0.247）](/post-attach/mame_cn_utf8.html)
