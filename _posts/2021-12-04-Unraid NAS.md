---
title: Unraid NAS
date: 2021-12-04 17:57:00 +0800
categories: [Note]
tags: [unraid,nas]
---

## 制作启动盘

没有什么难度，基本按照[官网的教程](https://wiki.unraid.net/Articles/Getting_Started#Getting_Started)走

1. 用质量好点的U盘，假冒伪劣产品有可能没序列号导致无法使用
1. [Unraid官网下载USB Creator](https://unraid.net/download)想白嫖也可以去找找开心版
1. 用USB Creator把Unraid系统刷进U盘
1. 插上主板，开机
1. 尝试默认的本地域名`http://tower`/`http://tower.local`，或者机器插上显示屏也能看到unraid获取的IP地址，实在不行上路由器查
1. 默认是root用户没密码直接就能进，进去之后立刻设置密码
1. 进去之后激活KEY，或者先试用30天

## 翻墙加速

写进go文件里，这样就会开机自动执行，否则重启后失效

```shell
nano /boot/config/go
```

社区应用大多来自github，把`raw.githubusercontent.com`写进`hosts`里

```shell
echo `199.232.28.133  raw.githubusercontent.com` >> /etc/hosts
echo `199.232.4.133   raw.githubusercontent.com` >> /etc/hosts
echo `199.232.4.133   raw.github.com` >> /etc/hosts
```

以及通过使用镜像站来实现docker加速

```shell
echo `{"registry-mirrors":["https://docker.mirrors.ustc.edu.cn/","https://hub-mirror.c.163.com/"]}` >> /etc/docker/daemon.json
```

## 注意点

我国网络运营商基本上封杀了以下端口，端口映射时注意

- 80
- 8080
- 443
- 445

## 插件

|插件名称|插件安装链接|社区论坛帖子链接|功能|  
|---|---|---|---|  
|Community Applications|<https://raw.githubusercontent.com/Squidly271/community.applications/master/plugins/community.applications.plg>|<https://forums.unraid.net/topic/38582-plug-in-community-applications/>|让Unraid内置社区插件浏览|
|Unassigned Devices|<https://raw.githubusercontent.com/Squidly271/community.applications/master/plugins/community.applications.plg>  <https://raw.githubusercontent.com/dlandon/unassigned.devices/master/unassigned.devices-plus.plg>|<https://forums.unraid.net/topic/92462-unassigned-devices-managing-disk-drives-and-remote-shares-outside-of-the-unraid-array/>|可以识别并管理没有加入到阵列里的储存设备，支持大部分文件系统，建议安装Plus以保证全部功能可用|

## Docker

|名称|Image|功能|  
|---|---|---|  
|ddns-route53|`crazymax/ddns-route53`|由于我是用Amazon Route 53管理域名的，所以动态DNS我就用这个Image了|
|CrushFTP10|`netlah/crushftp`|MFT(Managed File Transfer)方案  支持FTP/S、SFTP、HTTP/S(WebDAV)|
|qBittorrent|`johngong/qbittorrent`|BT下载  可选官版或qBittorrent-Enhanced-Edition版|
|caddy|`kylekingcdn/caddy-dns-route53`|加上了dns-route53模组的Caddy2  由于国内禁止80和443端口，所以默认使用HTTP challenge和TLS-ALPN challenge的Caddy2就没法正常申请证书了  拿来当反向代理的入口|
|FileZilla|`jlesage/filezilla`|FTP客户端  有时候从FTP服务器下一些比较大的文件又不想开一晚上电脑，就让NAS帮我下了|
