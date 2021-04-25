---
title: '树莓派4B+Aria2搭建离线下载服务器'
date: 2021-04-13 20:41:35
categories:
- 折腾
tags:
- 树莓派
- Aria2
- 离线下载
- 远程下载
- frp
---

![树莓派](/post-images/shu-mei-pai-4b-zhen-shi-shi-yong-ti-yan.jpg)

之前用 2012 年的老旧笔记本 ThinkPad E530c，做了一个 Aria2 下载服务器。

时间长了发现这玩意耗电还是挺可观的，遂买了树莓派 4B，作为替代。

美其名曰节能减排，实际就是按捺不住骚动的内心，实在是想着折腾点啥吧。

<!-- more -->

# 购置

本着能省则省，买了树莓派 4B 的 2G 版本，物品清单如下：

-   树莓派 4B（2G，298 元）
-   电源插头+电源线（媳妇碎屏华为的快充，0 元）
-   TF 卡（旧摄像头拆的，32G，0 元）
-   移动硬盘（旧笔记本拆的，1T，挂个硬盘盒，29 元）
-   亚克力外壳+风扇（19 元）
-   散热片（7 元）
-   读卡器（8 元）
-   Micro HDMI 转接头（9 元）

总计：350 元整。没算错吧哈哈。

# 开箱

在公司收到快递，迫不及待的打开包装，上手盘了一会。

散热片还是有必要的。一共五个，撕开胶贴，粘上即可。

外壳+风扇，拧几个螺丝搞定。

在公司还是不敢太嚣张，回家搞。

# 安装配置

## 烧录系统

１、下载安装 Raspberry Pi Imager（镜像烧录工具），或其他系统烧录工具。

[https://www.raspberrypi.org/software/](https://www.raspberrypi.org/software/)

２、下载系统镜像，烧录到 TF 卡上，以 Ubuntu Server 为例：

[https://cdimage.ubuntu.com/releases/20.04/release/](https://cdimage.ubuntu.com/releases/20.04/release/)

树莓派是 ARM 架构，切记一定要选择这个版本：

“Raspberry Pi Generic (64-bit ARM) preinstalled server image”

## 配置 Wifi

如果直接插网线连接路由器，可以略过这一步。

无线 Wifi 需要一些配置，在 TF 卡的“system-boot”分区，修改配置文件“network-config”。

配置文件中可能已经有一些配置项，只不过用井号#注释掉了，可以去掉注释，修改 SSID 和密码。

如下，千万注意缩进，四个空格为一个缩进。

```yaml
wifis:
    wlan0:
    dhcp4: true
    optional: true
    access-points:
    "你的无线SSID":
        password: "你的无线密码"
```

## 开机

TF 卡插入树莓派，接入键盘、Micro HDMI 连显示器，插上电源即启动。

正常情况首次启动会慢一些，启动完成后，输入默认的用户名密码：ubuntu/ubuntu，登录。

首次登录会提示修改初始密码，改完记录好。

## SSH

现在树莓派还连在显示器上：

```bash
ifconfig -a
```

查找其中类似于 192.168.1.\*或 192.168.0.\*的 IP。

当然也可以通过家中的路由器控制界面，来查看 IP。

知道它的 IP，就可以甩开键盘、显示器，自由奔放的 SSH 了：

```bash
ssh ubuntu@192.168.1.110
```

# Aria2

## frp

为了能让 Aria2 在外网访问实现远程下载，这里采用了 frp 端口映射到 VPS 的方式（如果你家里宽带有独立 IP 那更好）。

从以下地址，下载 frp 程序：

[https://github.com/fatedier/frp/releases](https://github.com/fatedier/frp/releases)

树莓派 4B 是 arm64 架构，所以需要分别下载 2 个：

frp_0.36.2_linux_arm64.tar.gz

frp_0.36.2_linux_amd64.tar.gz

### frps

将 frp_0.36.2_linux_amd64.tar.gz 部署到 VPS 的/usr/local/frps/文件夹。

编辑 frps 配置文件如下：

```ini
nano /usr/local/frps/frps.ini

[common]
bind_addr = 0.0.0.0
bind_port = 5443
kcp_bind_port = 5443
dashboard_port = 6443
dashboard_user = admin
dashboard_pwd = frps_1024
vhost_http_port = 8080
vhost_https_port = 8443
log_file = /usr/local/frps/frps.log
log_level = info
log_max_days = 3
token = frps的token随后要用
max_pool_count = 50
tcp_mux = true
```

编辑 frps 服务文件如下：

```bash
nano /etc/init.d/frps
```

```bash
#! /bin/bash

PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
ProgramName="Frps"
ProgramPath="/usr/local/frps"
NAME=frps
BIN=${ProgramPath}/${NAME}
CONFIGFILE=${ProgramPath}/frps.ini
SCRIPTNAME=/etc/init.d/${NAME}
version="1.1"
program_version=`${BIN} --version`
RET_VAL=0

[ -x ${BIN} ] || exit 0
strLog=""
fun_clangcn()
{
    echo ""
    echo "+--------------------------------------------------+"
    echo "|        Manager for ${ProgramName}, Written by Clang        |"
    echo "+--------------------------------------------------+"
    echo "| Intro: http://koolshare.cn/thread-65379-1-1.html |"
    echo "+--------------------------------------------------+"
    echo ""
}

fun_check_run(){
    PID=`ps -ef | grep -v grep | grep -i "${BIN}" | awk '{print $2}'`
    if [ ! -z $PID ]; then
        return 0
    else
        return 1
    fi
}
fun_load_config(){
    if [ ! -r ${CONFIGFILE} ]; then
        echo "config file ${CONFIGFILE} not found"
        return 1
    fi
}
fun_start()
{
    if [ "${arg1}" = "start" ]; then
      fun_clangcn
    fi
    if fun_check_run; then
        echo "${ProgramName} (pid $PID) already running."
        return 0
    fi
    fun_load_config
    echo -n "Starting ${ProgramName}(${program_version})..."
    ${BIN} -c ${CONFIGFILE} >/dev/null 2>&1 &
    sleep 1
    if ! fun_check_run; then
        echo "start failed"
        return 1
    fi
    echo " done"
    echo "${ProgramName} (pid $PID)is running."
    return 0
}

fun_stop(){
    if [ "${arg1}" = "stop" ] || [ "${arg1}" = "restart" ]; then
      fun_clangcn
    fi
    if fun_check_run; then
        echo -n "Stoping ${ProgramName} (pid $PID)... "
        kill $PID
        if [ "$?" != 0 ] ; then
            echo " failed"
            return 1
        else
            echo " done"
        fi
    else
        echo "${ProgramName} is not running."
    fi
    return 0
}
fun_restart(){
    fun_stop
    fun_start
}
fun_status(){
    PID=`ps -ef | grep -v grep | grep -i "${BIN}" | awk '{print $2}'`
    if [ ! -z $PID ]; then
        echo "${ProgramName} (pid $PID) is running..."
    else
        echo "${ProgramName} is stopped"
        exit 0
    fi
}
checkos(){
    if grep -Eqi "CentOS" /etc/issue || grep -Eq "CentOS" /etc/*-release; then
        OS=CentOS
    elif grep -Eqi "Debian" /etc/issue || grep -Eq "Debian" /etc/*-release; then
        OS=Debian
    elif grep -Eqi "Ubuntu" /etc/issue || grep -Eq "Ubuntu" /etc/*-release; then
        OS=Ubuntu
    elif grep -Eqi "Alpine" /etc/issue || grep -Eq "Alpine" /etc/*-release; then
        OS=Alpine
    else
        echo "Not support OS, Please reinstall OS and retry!"
        return 1
    fi
}
fun_config(){
    if [ -s ${CONFIGFILE} ]; then
        vi ${CONFIGFILE}
    else
        echo "${ProgramName} configuration file not found!"
        return 1
    fi
}
fun_version(){
    echo "${ProgramName} version ${program_version}"
    return 0
}
fun_help(){
    ${BIN} --help
    return 0
}

arg1=$1
[  -z ${arg1} ]
case "${arg1}" in
    start|stop|restart|status|config)
        fun_${arg1}
    ;;
    [vV][eE][rR][sS][iI][oO][nN]|-[vV][eE][rR][sS][iI][oO][nN]|--[vV][eE][rR][sS][iI][oO][nN]|-[vV]|--[vV])
        fun_version
    ;;
    [Cc]|[Cc][Oo][Nn][Ff]|[Cc][Oo][Nn][Ff][Ii][Gg]|-[Cc]|-[Cc][Oo][Nn][Ff]|-[Cc][Oo][Nn][Ff][Ii][Gg]|--[Cc]|--[Cc][Oo][Nn][Ff]|--[Cc][Oo][Nn][Ff][Ii][Gg])
        fun_config
    ;;
    [Hh]|[Hh][Ee][Ll][Pp]|-[Hh]|-[Hh][Ee][Ll][Pp]|--[Hh]|--[Hh][Ee][Ll][Pp])
        fun_help
    ;;
    *)
        fun_clangcn
        echo "Usage: $SCRIPTNAME {start|stop|restart|status|config|version}"
        RET_VAL=1
    ;;
esac
exit $RET_VAL
```

最后刷新一下服务：

```bash
systemctl daemon-reload
systemctl start frps
systemctl status frps
```

### frpc

将 frp_0.36.2_linux_arm64.tar.gz 部署到树莓派的/usr/local/frpc/文件夹。

编辑 frpc 配置文件如下：

```bash
nano /usr/local/frpc/frpc.ini
```

```ini
[common]
server_addr = VPS的IP地址
server_port = 5443
authentication_method = token
token = VPS上frps的token

[aria2]
type = tcp
local_ip=127.0.0.1
local_port = 6800
remote_port = 8600
```

编辑 frpc 服务文件如下：

```ini
[Unit]
Description=FRP Client Daemon
After=network.target
Wants=network.target

[Service]
Type=simple
ExecStart=/usr/local/frpc/frpc -c /usr/local/frpc/frpc.ini
Restart=always
RestartSec=20s
LimitNOFILE=infinity

[Install]
WantedBy=multi-user.target
```

最后刷新一下服务，启动服务，查看服务状态是否正常

```bash
systemctl daemon-reload
systemctl start frpc
systemctl status frpc
```

## 部署 aria2

强烈推荐使用 P3TERX 的 aria2.sh 脚本来安装 aria2，非常简单：

[https://github.com/P3TERX/aria2.sh](https://github.com/P3TERX/aria2.sh)

```bash
apt install wget curl ca-certificates
wget -N git.io/aria2.sh && chmod +x aria2.sh
./aria2.sh
```

后续步骤按提示操作即可，最后成功了，会有如下输出：

```
Aria2 简单配置信息：

 IPv4 地址      : 123.123.123.123
 IPv6 地址      : IPv6 地址检测失败
 RPC 端口       : 6800
 RPC 密钥       : Aria2_1024
 下载目录       : /share/downloads
 AriaNg 链接    : http://ariang.js.org/#!/settings/rpc/set/ws/123.123.123.123/6800/jsonrpc/token
```

将最后一行地址中的“123.123.123.123”换成 VPS 的 IP 地址，即可远程访问 Aria2。

![Aria2](https://kaifeiji.cc/post-images/1619180597689.png)

网络链路如下：

浏览器 -> ariang.js.org 提供的 AriaNg 前端 -> VPS 的 frps 转发 -> 树莓派的 frpc 接收 -> aria2 程序交互

## 部署 AriaNg

也可以不用 ariang.js.org 的前端，自己部署一个 AriaNg。

从 Github 下载 AriaNg（AllInOne 是单个 html 文件），部署到 VPS 的 Nginx 或 Apache 即可：

[https://github.com/mayswind/AriaNg/releases](https://github.com/mayswind/AriaNg/releases)
