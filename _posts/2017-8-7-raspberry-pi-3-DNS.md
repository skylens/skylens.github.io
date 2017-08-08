---
layout: post_layout
title: 使用 Raspberry Pi 3 搭建本地 DNS 服务器
time: 2017年8月7日 星期一
location: 昆明
pulished: true
excerpt_separator: "```"
---

### 前言

使用 Raspberry Pi 3 搭建本地的 DNS server，来解决 DNS 污染的问题，并加速 DNS 查询，从而提升上网体验，当然了你也可以使用一些无污染的 DNS ，比如中科大的 DNS （202.38.93.153,202.141.162.123）

### 配置

+ dnsmasq 

```shell
$ sudo apt-get install dnsmasq
$ cd /etc/
$ sudo cp dnsmasq.conf dnsmasq.conf.orig
$ sudo vim dnsmasq.conf

resolv-file=/etc/resolv.dnsmasq.conf
strict-order
listen-address=192.168.1.102,127.0.0.1

$ sudo vim resolv.dnsmasq.conf

nameserver 202.38.93.153 
nameserver 202.141.162.123

$ sudo vim resolv.conf

nameserver 127.0.0.1

$ sudo chattr +i resolv.conf
$ sudo /etc/init.d/dnsmasq reload
$ sudo systemctl enable dnsmasq
```

### 测试

```shell
$ sudo apt-get install dnsutils
$ dig @127.0.0.1 t.co
$ nslookup www.google.com 127.0.0.1
```