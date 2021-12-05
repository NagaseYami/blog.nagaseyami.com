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
1. 尝试默认的本地域名`http://tower`或者`http://tower.local`，或者机器插上显示屏也能看到unraid获取的IP地址，实在不行上路由器查
1. 默认是root用户没密码直接就能进，进去之后立刻设置密码
1. 进去之后激活KEY，或者先试用30天

## 翻墙加速

写进go文件里，这样就会开机自动执行，否则重启后失效

```shell
nano /boot/config/go
```
{: file="/boot/config/go" .nolineno}

社区应用大多来自github，把`raw.githubusercontent.com`写进`hosts`里

```shell
echo `199.232.28.133  raw.githubusercontent.com` >> /etc/hosts
echo `199.232.4.133   raw.githubusercontent.com` >> /etc/hosts
echo `199.232.4.133   raw.github.com` >> /etc/hosts
```
{: file="/boot/config/go" .nolineno}

以及通过使用镜像站来实现docker加速

```shell
echo `{"registry-mirrors":["https://docker.mirrors.ustc.edu.cn/","https://hub-mirror.c.163.com/"]}` >> /etc/docker/daemon.json
```
{: file="/boot/config/go" .nolineno}

## 注意点

我国网络运营商基本上封杀了以下端口，端口映射时注意

- 80
- 8080
- 443
- 445

## 插件

### Community Applications

- [社区论坛贴](https://forums.unraid.net/topic/38582-plug-in-community-applications/)
- 安装链接
  - `https://raw.githubusercontent.com/Squidly271/community.applications/master/plugins/community.applications.plg`

安装后就能从unraid的WebUI界面里直接浏览下载社区插件了

### Unassigned Devices

- [社区论坛贴](https://forums.unraid.net/topic/92462-unassigned-devices-managing-disk-drives-and-remote-shares-outside-of-the-unraid-array/)
- 安装链接
  - `https://raw.githubusercontent.com/dlandon/unassigned.devices/master/unassigned.devices.plg`
  - `https://raw.githubusercontent.com/dlandon/unassigned.devices/master/unassigned.devices-plus.plg`

可以识别并管理没有加入到阵列里的储存设备，支持大部分文件系统，建议安装Plus以保证全部功能可用

### Nerd Pack

- [社区论坛帖](https://forums.unraid.net/topic/35866-unraid-6-nerdpack-cli-tools-iftop-iotop-screen-kbd-etc/)
- 安装链接
  - `https://raw.githubusercontent.com/dmacias72/unRAID-NerdPack/master/plugin/NerdPack.plg`

unraid是基于Slackware的，所以不存在apt或者yum这种Package Manager  
如果需要一些CLI工具比如tmux，可以从这个插件里下载

### Dynamix File Integrity

- [社区论坛帖](https://forums.unraid.net/topic/43290-dynamix-file-integrity-plugin/)
- 安装链接
  - `https://raw.githubusercontent.com/bergware/dynamix/master/unRAIDv6/dynamix.file.integrity.plg`

可以实时检测磁盘中的文件完整性，支持SHA2、MD5、BLAKE3校验算法，可以设置定期检查

### Dynamix System Temperature

- 社区论坛帖好像没有
- 安装链接
  - `https://github.com/bergware/dynamix/blob/master/unRAIDv6/dynamix.system.temp.plg`

检测CPU温度和主板温度的，可以显示在Dashboard和WebUI下方的状态条里  
具体操作看[官方教程](https://wiki.unraid.net/Setting_up_CPU_and_board_temperature_sensing)

### Open Files

- [社区论坛贴](https://forums.unraid.net/topic/41196-open-files-plugin-can-help-with-troubleshooting-why-server-wont-shut-down/)
- 安装链接
  - `https://raw.githubusercontent.com/dlandon/open.files/master/open.files.plg`

可以方便直观地查看哪些进程在使用哪些文件，以及这些被占用的文件会不会影响关机/停止整列

## Docker

### crazymax/ddns-route53

- [文档](https://crazymax.dev/ddns-route53/)

由于我是用Amazon Route 53管理域名的，所以动态DNS我就用这个Image了

### kylekingcdn/caddy-dns-route53

- 文档
  - [Caddy](https://hub.docker.com/_/caddy)
  - [Caddy Dns Route 53](https://github.com/caddy-dns/route53)
  - [Route 53验证设置](https://github.com/libdns/route53)

加上了dns-route53模组的Caddy2  
由于国内禁止80和443端口，所以默认使用HTTP challenge和TLS-ALPN challenge的Caddy2就没法正常申请证书了，得用DNS challenge  
拿来当反向代理的入口

### netlah/crushftp

- [文档](https://hub.docker.com/r/netlah/crushftp)

MFT(Managed File Transfer)方案  支持FTP/S、SFTP、HTTP/S(WebDAV)，企业级用户管理  

### johngong/qbittorrent

- [文档](https://hub.docker.com/r/johngong/qbittorrent)

BT下载  
可选官版或qBittorrent-Enhanced-Edition版

### jlesage/filezilla

- [文档](https://github.com/jlesage/docker-filezilla)

FTP客户端  
有时候从FTP服务器下一些比较大的文件又不想开一晚上电脑，就让NAS帮我下了
> 默认中日韩（CJK）文字乱码，可以通过环境变量安装CJK字体，具体看文档
