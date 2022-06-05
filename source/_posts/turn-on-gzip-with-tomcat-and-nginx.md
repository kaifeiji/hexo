---
title: Tomcat和Nginx启用gzip
date: 2022-06-03 00:42:02
categories:
  - 运维
tags:
  - gzip
  - Tomcat
  - Nginx
---

启用 gzip，对于大型应用而言，是非常必要的。

实测 10M 的 js，压缩到了 2M，性能提升 5 倍！

<!-- more -->

未启用 gzip，10M，加载 14 秒：

![](/post-images/turn-on-gzip-with-tomcat-and-nginx-2022-06-03-00-54-33.png)

启用 gzip，2M，加载 4 秒：

![](/post-images/turn-on-gzip-with-tomcat-and-nginx-2022-06-03-00-55-27.png)

Response Headers 包含：

`Content-Encoding: gzip`

Request Headers 包含：

`Accept-Encoding: gzip, deflate`

## Tomcat 配置

修改 Tomcat 的 conf/server.xml

```xml
<Connector port="8080"
  protocol="HTTP/1.1"
  connectionTimeout="20000"
  redirectPort="8443"
  useSendfile="false"
  compression="on"
  compressionMinSize="2048"
  noCompressionUserAgents="gozilla, traviata"
  compressableMimeType="text/html,text/xml,text/javascript,application/javascript,text/css,text/json"/>
```

具体参数含义见[Tomcat 配置](https://tomcat.apache.org/tomcat-5.5-doc/config/http.html#Standard%20Implementation)

## Nginx 配置

直接代理 Tomcat 的 Nginx，作如下配置：

```nginx
server{
    gzip on;
    gzip_comp_level 6;
    gzip_min_length 2048;
    gzip_types text/html text/xml text/javascript application/javascript text/css text/json;
    gzip_http_version 1.0;
    gzip_vary on;
}
```

具体参数含义见[nginx 配置](https://nginx.org/en/docs/http/ngx_http_gzip_module.html)

**多层 Nginx 的网络结构，外层的 Nginx，无论开启或关闭 gzip，内层 Nginx 的 gzip 仍然生效。**
