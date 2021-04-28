---
title: '告别OneDrive，拥抱Nextcloud'
date: 2020-09-08 08:59:28
categories:
- 折腾
tags:
- 网盘
- OneDrive
- Nextcloud
- Ubuntu
---

告别OneDrive，拥抱Nextcloud

<!-- more -->

![Nextcloud](/post-images/Nextcloud.png)

**2021-04-26更新**

其实Nextcloud官方早已经有更加自动化的安装方式，只不过我没有发现：

[Nextcloud VM](https://github.com/nextcloud/vm)

Nextcloud VM基于Bash脚本安装，会根据机器的实际情况采用最优配置。

另外还附带了一些可选功能，比如自动配置SSL证书（Let's Encrypt）、Webmin服务器管理、Adminer数据库管理等工具。

也有一个小问题：最好一次安装成功，第二次安装时，如果依赖软件没卸载干净，会有各种冲突。

我就是在逼不得已的情况下，重装了系统，第三次安装才成功……

---

# 蛋疼的 OneDrive APP 速度

从前年开始订阅的 Office 365，白送 OneDrive 1T 空间，本着不用白不用的原则，把工作和个人的内容都扔到上边，三端同步，以防万一。

但是不知道为什么，工作笔记本和家里的台式机速度都很正常，但手机 APP 却慢的要死。

说不清是手机网络的事儿，还是 OneDrive 服务器的问题。

一番攻略，排除了第三方网盘服务，在个人网盘方案筛选一遍，找到了 Nextcloud。

# Nextcloud 功能介绍

Nextcloud 支持浏览器 Web 访问，客户端有 Windows、macOS、Linux。

具有网盘的基本功能：上传、存储、下载、预览、分享。

另外还有很多应用（插件）：日历、邮件、社交、提醒等等。

这个东西适合喜欢折腾的人，安逸的人建议直接购买群晖等商业 NAS。

![目录](/post-images/nextcloudindex.png)

# 安装和配置

如果英文好，可以跳过这篇文章，直接查看[官方文档](https://docs.nextcloud.com/server/19/admin_manual/)

## 部署环境

硬件：ThinkPad E530C（2013 年）
操作系统：Ubuntu 20

## Snap 安装

推荐直接[Snap 安装](https://docs.nextcloud.com/server/19/admin_manual/installation/source_installation.html#installing-via-snap-packages)，快速无痛。

Ubuntu 20 自带 Snap，如果没有 Snap，需要先安装 Snap：

```bash
sudo apt update
sudo apt install snap
```

执行 Snap 安装 Nextcloud：

```bash
sudo snap install nextcloud
```

安装完成后，Nextcloud 自动启动（80 端口），可以在直接在浏览器中访问[http://localhost](http://localhost)。

在第一次开启登录界面时，根据提示设置管理员用户、密码，并指定存储位置（后续再修改会很困难）。

![登录](/post-images/nextcloudlogin.png)

到此处，一个局域网可运行的玩具就部署完成了。

# 映射到互联网

如果要映射到互联网，必须满足 3 个条件之一：

1、家里的宽带有公网 IP，Nextcloud 所在的服务器能通过公网 IP 暴露到互联网

2、买了带公网 IP 的云服务器，通过 frp 把 Nextcloud 转发到互联网

3、购买 nat123、natapp 等第三方服务，转发本地的 Nextcloud

最终的结果，就是在任意一台互联网电脑，能通过 IP 或域名访问到本地的 Nextcloud。

## 域名配置

为域名添加记录：公网 IP 地址或 nat 服务商提供的域名，实现用域名访问 Nextcloud。

## 修改端口

用 Snap 的 Set 命令设置 Nextcloud 的端口，主要是为了后续 Ngxin 统一转发 80。

```bash
sudo snap set nextcloud ports.http=8080
```

## 申请 SSL 证书

在腾讯云或阿里云申请免费版 SSL 证书，参考腾讯云文档：

[https://cloud.tencent.com/document/product/400/35244](https://cloud.tencent.com/document/product/400/35244)

## 配置 Nginx 的 https 转发

首先安装 Nginx：

```bash
sudo apt install nginx
```

修改 Nginx 配置：

```nginx
sudo vi /etc/nginx/nginx.conf
```

需要注意的点：

1、必须注释这一行：include /etc/nginx/sites-enabled/\*;

2、安全证书要设置成上一步申请的 Nginx 版证书

```nginx
ssl_certificate /home/root/1_www.example.com_bundle.crt;
ssl_certificate_key /home/root/2_www.example.com.key;
```

## Nextcloud 配置

在设置好 Nginx 转发后，最后，还需要修改 Nextcloud 的配置文件：

```bash
cd /var/snap/nextcloud/current/nextcloud/config
sudo vi config.php
```

需要注意的点：

1、trusted_domains 填写自己的 IP 或域名

```php
  'trusted_domains' =>
  array (
    0 => 'localhost:8080',
    1 => '192.168.1.109:8080',
    2 => 'i.kaifeiji.cc',
  ),
```

2、version 下一行必须添加 overwriteprotocol，否则会报错

```php
  'overwriteprotocol' => 'https',
```

> [https://techoverflow.net/2020/02/09/how-to-fix-nextcloud-refused-to-send-form-data-to-login-v2-grant-because-it-violates-the-following-content-security-policy-directive-form-action-self/](https://techoverflow.net/2020/02/09/how-to-fix-nextcloud-refused-to-send-form-data-to-login-v2-grant-because-it-violates-the-following-content-security-policy-directive-form-action-self/)

# 参考

把 Nginx 的 nginx.conf 和 Nextcloud 的 config.php 的整个内容粘贴在这里：

## nginx.conf

```nginx
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
	worker_connections 768;
	# multi_accept on;
}

http {

	##
	# Basic Settings
	##

	sendfile on;
	tcp_nopush on;
	tcp_nodelay on;
	keepalive_timeout 65;
	types_hash_max_size 2048;
	# server_tokens off;

	# server_names_hash_bucket_size 64;
	# server_name_in_redirect off;

	include /etc/nginx/mime.types;
	default_type application/octet-stream;

	##
	# SSL Settings
	##

	ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3; # Dropping SSLv3, ref: POODLE
	ssl_prefer_server_ciphers on;

	##
	# Logging Settings
	##

	access_log /var/log/nginx/access.log;
	error_log /var/log/nginx/error.log;

	##
	# Gzip Settings
	##

	gzip on;

	# gzip_vary on;
	# gzip_proxied any;
	# gzip_comp_level 6;
	# gzip_buffers 16 8k;
	# gzip_http_version 1.1;
	# gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

	##
	# Virtual Host Configs
	##

	include /etc/nginx/conf.d/*.conf;
    #注意！下边这行必须要注释掉
	#include /etc/nginx/sites-enabled/*;

	upstream php-handler {
		server 127.0.0.1:8080;
		#server unix:/var/run/php/php7.4-fpm.sock;
	}

	server {
		listen 80;
		listen [::]:80;
		server_name www.example.com;
		return 301 https://$server_name:443$request_uri;
	}

	server {
		listen 443 ssl http2;
		listen [::]:443 ssl http2;
		server_name www.example.com;

		# Use Mozilla's guidelines for SSL/TLS settings
		# https://mozilla.github.io/server-side-tls/ssl-config-generator/
		# NOTE: some settings below might be redundant
		ssl_certificate /home/kaifeiji/1_www.example.com_bundle.crt;
		ssl_certificate_key /home/kaifeiji/2_www.example.com.key;
		ssl_session_timeout 5m;
		ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
		ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
		ssl_prefer_server_ciphers on;

		# Add headers to serve security related headers
		# Before enabling Strict-Transport-Security headers please read into this
		# topic first.
		#add_header Strict-Transport-Security "max-age=15768000; includeSubDomains; preload;" always;
		#
		# WARNING: Only add the preload option once you read about
		# the consequences in https://hstspreload.org/. This option
		# will add the domain to a hardcoded list that is shipped
		# in all major browsers and getting removed from this list
		# could take several months.
        add_header Strict-Transport-Security 'max-age=15552000';
		add_header Referrer-Policy "no-referrer" always;
		#add_header X-Content-Type-Options "nosniff" always;
		#add_header X-Download-Options "noopen" always;
		#add_header X-Frame-Options "SAMEORIGIN" always;
		#add_header X-Permitted-Cross-Domain-Policies "none" always;
		#add_header X-Robots-Tag "none" always;
		#add_header X-XSS-Protection "1; mode=block" always;

		# Remove X-Powered-By, which is an information leak
		fastcgi_hide_header X-Powered-By;

		location = /robots.txt {
			allow all;
			log_not_found off;
			access_log off;
		}

		location = /.well-known/carddav {
		  return 301 $scheme://$host:$server_port/remote.php/dav;
		}
		location = /.well-known/caldav {
		  return 301 $scheme://$host:$server_port/remote.php/dav;
		}

		# set max upload size
		client_max_body_size 512M;
		fastcgi_buffers 64 4K;

		# Enable gzip but do not remove ETag headers
		gzip on;
		gzip_vary on;
		gzip_comp_level 4;
		gzip_min_length 256;
		gzip_proxied expired no-cache no-store private no_last_modified no_etag auth;
		gzip_types application/atom+xml application/javascript application/json application/ld+json application/manifest+json application/rss+xml application/vnd.geo+json application/vnd.ms-fontobject application/x-font-ttf application/x-web-app-manifest+json application/xhtml+xml application/xml font/opentype image/bmp image/svg+xml image/x-icon text/cache-manifest text/css text/plain text/vcard text/vnd.rim.location.xloc text/vtt text/x-component text/x-cross-domain-policy;

		location / {
			proxy_set_header Host $host;
			proxy_pass http://localhost:8080/;
		}
	}
}
```

## config.php

```php
<?php
$CONFIG = array (
  'apps_paths' =>
  array (
    0 =>
    array (
      'path' => '/snap/nextcloud/current/htdocs/apps',
      'url' => '/apps',
      'writable' => false,
    ),
    1 =>
    array (
      'path' => '/var/snap/nextcloud/current/nextcloud/extra-apps',
      'url' => '/extra-apps',
      'writable' => true,
    ),
  ),
  'supportedatabases' =>
  array (
    0 => 'mysql',
  ),
  'memcache.locking' => '\\OC\\Memcache\\Redis',
  'memcache.local' => '\\OC\\Memcache\\Redis',
  'redis' =>
  array (
    'host' => '/tmp/sockets/redis.sock',
    'port' => 0,
  ),
  'instanceid' => 'ocwn0882ai4o',
  'passwordsalt' => 'avzs/iopCK+p/j0OCLSL5mLXufnZw7',
  'secret' => 'sdJeYJHugREIPOomTLI8rhZOxNFqpcVWB1/EziKf9ARZOO/Y',
  'trusted_domains' =>
  array (
    0 => 'localhost:8080',
    1 => '192.168.1.109:8080',
    2 => 'www.example.com',
  ),
  'datadirectory' => '/var/snap/nextcloud/common/nextcloud/data',
  'dbtype' => 'mysql',
  'version' => '19.0.2.2',
  'overwriteprotocol' => 'https',
  'overwrite.cli.url' => 'http://localhost:8080',
  'dbname' => 'nextcloud',
  'dbhost' => 'localhost:/tmp/sockets/mysql.sock',
  'dbport' => '',
  'dbtableprefix' => 'oc_',
  'mysql.utf8mb4' => true,
  'dbuser' => 'nextcloud',
  'dbpassword' => '545Sa7ZEULmy2Q_LApv8pvVAW46tv1SMn1nWpBmstAwSiKkAWfywogV4kwYE_Zce',
  'installed' => true,
);

```
