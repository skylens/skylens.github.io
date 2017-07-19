---
layout: post_layout
title: pptp VPN 服务器搭建
time: 2017年7月19日 星期三
location: 昆明
pulished: true
excerpt_separator: "```"
---

### 前言

在ubuntu server 14.04上搭建pptp VPN服务

### 安装

+ pptpd安装

```shell
$ sudo apt-get install pptpd
```

+ 设置localip和remoteip

(根据实际情况来设置)

```shell
# nano /etc/pptpd.conf

localip 192.168.0.1
remoteip 192.168.0.100-200

```

+ 用户名密码设置

```shell
# nano /etc/ppp/chap-secrets

admin pptpd admin123 *

```

+ DNS服务器设置

```shell
# nano /etc/ppp/pptpd-options

ms-dns 114.114.114.114
ms-dns 114.114.115.115

```

+ 重启服务

```shell
# service pptpd restart
```

+ 

```shell
# nano /etc/sysctl.conf

net.ipv4.ip_forward=1

# sysctl -p
```

+ iptables设置

```shell

# iptables -t nat -A POSTROUTING -s 192.168.0.0/24 -j SNAT --to-source VPS的IP地址

# iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
# iptables --table nat --append POSTROUTING --out-interface ppp0 -j MASQUERADE
# iptables -I INPUT -s 192.168.0.0/24 -i ppp0 -j ACCEPT
# iptables --append FORWARD --in-interface eth0 -j ACCEPT
# iptables-save > firewall
```
