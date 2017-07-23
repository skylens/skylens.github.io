---
layout: post_layout
title: 编译安装openvpn
time: 2017年7月23日 星期天
location: 昆明
pulished: true
excerpt_separator: "```"
---

### 前言

在centos7(需要两块网卡)上编译安装openvpn，采用VMware虚拟机做为实验平台，CentOS 7 作为 VPN Server，windows10作为客户端

### 开始

+ 规划

|系统|网卡|名称|IP地址|网段|
|:--:|:--:|:--:|:----:|:--:|
||eth0||192.168.42.153|192.168.42.0/24|
|CentOS7
