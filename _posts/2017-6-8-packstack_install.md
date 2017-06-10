---
layout: post_layout
title: packstack 安装 openstack
time: 2017年6月8日 星期四
location: 昆明
pulished: true
excerpt_separator: "```"
---

### packstack (rdo)

packstack(或者说rdo)是一个RedHat的OpenStack社区维护的OpenStack部署方案。

### 环境

CentOS 7 (4GB RAM + 60GB 磁盘) + OpenStack ocata 版

### 系统基础设置

```shell
# dhclient eno16777736
# yum list
# yum install -y openssh-serevr vim epel-release
# vim /etc/ssh/sshd_config
//设置允许root登录
# servic sshd restart
//最小安装默认没有安Firewall
# systemctl disable firewalld
# systemctl stop firewalld
# systemctl disable NetworkManager
# systemctl stop NetworkManager
# systemctl enable network
# systemctl start network
```

### 安装

```shell
# yum install -y centos-release-openstack-ocata
# yum update -y
# yum install -y openstack-packstack
# packstack --allinone
```