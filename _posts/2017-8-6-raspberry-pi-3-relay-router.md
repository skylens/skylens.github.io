---
layout: post_layout
title: 把 Raspberry Pi 3 作为中继路由器
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
$ sudo ifup wlan0
```

+ 开启内核转发

```shell
$ sudo vim /etc/sysctl.d/99-sysctl.conf 
net.ipv4.ip_forward = 1
$ sudo sysctl -p
net.ipv4.ip_forward = 1
$ sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
```

+ 安装 hostapd

```shell
$ sudo apt-get install hostapd -y
$ sudo vim /etc/hostapd/hostapd.conf

interface=wlan0
driver=nl80211
# ssid
ssid=RaspberryAP
hw_mode=g
channel=6
#ieee80211n=1
macaddr_acl=0
auth_algs=1
ignore_broadcast_ssid=0
wpa=2
# password
wpa_passphrase=1234567890
wpa_key_mgmt=WPA-PSK
#wpa_pairwise=TKIP
rsn_pairwise=CCMP
ieee80211n=1          # 802.11n support
wmm_enabled=1         # QoS support
ht_capab=[HT40][SHORT-GI-20][DSSS_CCK-40]

先不启动服务
```

+ 安装 DHCP

1. dnsmasq 的 DHCP 

```shell
$ sudo apt-get install dnsmasq
$ cd /etc/
$ sudo cp dnsmasq.conf dnsmasq.conf.orig
$ sudo vim dnsmasq.conf

interface=wlan0
bind-interfaces
dhcp-range=192.168.10.50,192.168.10.150,12h
$ sudo /etc/init.d/dnsmasq reload
$ sudo systemctl enable dnsmasq.service
```

2. udhcpd 的 DHCP

```shell
$ sudo apt-get install udhcpd -y
$ sudo vim /etc/udhcpd.conf
$ sudo systemctl start udhcpd.service
$ sudo systemctl enable udhcpd.service
```

### 测试

```shell
$ sudo hostapd -d /etc/hostapd/hostapd.conf   #检查是否能连接上
$ sudo vim /etc/default/hostapd

DAEMON_CONF="/etc/hostapd/hostapd.conf"

$ sudo systemctl start hostapd.service
$ sudo systemctl enable hostapd.service
```

连接 RaspberryAP ，看能不能上网


### 链接

[rPi3-ap-setup.sh](https://gist.github.com/Lewiscowles1986/fecd4de0b45b2029c390)

[Raspberry Pi 3 - Configuring it as wireless access point -AP Mode](https://wiki.alpinelinux.org/wiki/Raspberry_Pi_3_-_Configuring_it_as_wireless_access_point_-AP_Mode)