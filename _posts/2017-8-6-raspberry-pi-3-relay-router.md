---
layout: post_layout
title: 把 Raspberry Pi 3 作为中中继路由器
time: 2017年8月6日 星期天
location: 昆明
pulished: true
excerpt_separator: "```"
---

### 前言

把 Raspberry Pi 3 作为中继路由器，Raspberry Pi 3 有一个以太网接口，搭载了一块无线网卡，满足了用来作中继路由器的条件

### 准备

+ 设置无线网卡使用静态 IP

```shell
$ sudo nano /etc/network/interfaces

allow-hotplug wlan0
#iface wlan0 inet manual
#    wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf
iface wlan0 inet static
     address 192.168.10.1
     netmask 255.255.255.0
     network 192.168.10.0
     broadcast 192.168.10.255     
#allow-hotplug wlan1
#iface wlan1 inet manual
#    wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf

$ sudo service networking restart
$ sudo /etc/init.d/network restart
```

+ 安装 hostapd

```shell
$ sudo apt-get install hostapd -y
$ sudo vim /etc/hostapd/hostapd.conf
$ sudo systemctl start hostapd.service
$ sudo systemctl enable hostapd.service
```

+ 安装 DHCP

1. dnsmasq 的 DHCP 

```shell
$ sudo apt-get install dnsmasq
$ cd /etc/
$ sudo cp dnsmasq.conf dnsmasq.conf.orig
```

2. udhcpd 的 DHCP

```shell
$ sudo apt-get install udhcpd -y
$ sudo vim /etc/udhcpd.conf
$ sudo systemctl start udhcpd.service
$ sudo systemctl enable udhcpd.service
```