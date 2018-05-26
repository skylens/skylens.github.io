---
layout: post_layout
title: 使用 kickstart 自动安装 CentOS
time: 2018年5月26日 星期六
location: 昆明
pulished: true
excerpt_separator: "```"
---

### 前言

许多系统管理员都希望能够使用自动化安装的方式来在他们的机器上安装Fedora或红帽企业版Linux。为了满足这样的需求，红帽公司创建了kickstart安装方式。通过使用kickstart，系统管理员能够创建一个单独的、包含安装过程中遇到的所有问题答案的文件。

### 操作

参照手动安装完 `CentOS 7` 后生成的这个文件 `anaconda-ks.cfg` 编写 `kickstart` 应答文件。

`kickstart` 应答文件示例：

```
# platform=x86, AMD64, or Intel EM64T
# version=DEVEL
# Install OS instead of upgrade
install
# Keyboard layouts
keyboard 'us'
# Root password
rootpw --plaintext 1
# System language
lang en_US
# System authorization information
auth  --useshadow  --passalgo=sha512
# Use network installation
url --url="http://mirrors.aliyun.com/centos/7/os/x86_64/"
# Use text mode install
text
# SELinux configuration
selinux --disabled
# Do not configure the X Window System
skipx
# Firewall configuration
firewall --disabled
# System services
services --disabled="NetworkManager"
services --enabled="network"
# Reboot after installation
reboot
# Network information
network  --bootproto=dhcp --device=eth0 --ipv6=auto --activate
network  --bootproto=dhcp --hostname=centos7.localdomain
# System timezone
timezone Asia/Shanghai
# System bootloader configuration
# use eth0 
bootloader --append="net.ifnames=0 biosdevname=0 crashkernel=auto" --location=mbr --boot-drive=sda
autopart --type=lvm
# Partition clearing information
clearpart --none --initlabel

%packages
@base
@core
@development
bash-completion
bind-utils
chrony
git
kexec-tools
nano
net-tools
tmux
tree
vim
wget

%end

%post --interpreter=/bin/bash
# services
(
rm -f /etc/sysconfig/network-scripts/ifcfg-ens*
curl -o /etc/sysconfig/network-scripts/ifcfg-eth0 https://files.skylens.co/ifcfg-eth0
systemctl disable NetworkManager
systemctl enable network
systemctl enable chronyd
systemctl disable postfix
# del users
userdel adm
userdel lp
userdel sync
userdel shutdown
userdel halt
userdel news
userdel uucp
userdel operator
userdel games
userdel gopher
userdel ftp
# del group
groupdel adm
groupdel lp
groupdel news
groupdel uucp
groupdel games
groupdel dip
groupdel pppusers
# install 
yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
yum install -y lrzsz psmisc htop nmap tcpdump
)2>1 >> /root/post-install.log
%end
```

当然也可以使用 `system-config-kickstart` 来生成 `kickstart` 应答文件。

从镜像启动选择 `Install CentOS 7`, 不按回车，按 `Tab`，指定 `kickstart` 应答文件，在末尾添加 `ks=https://example.com/centos7.cfg`


1. [https://fedoraproject.org/wiki/Anaconda/Kickstart/zh-cn](https://fedoraproject.org/wiki/Anaconda/Kickstart/zh-cn)

2. [http://debugo.com/kickstart-install-centos/](http://debugo.com/kickstart-install-centos/)

3. [http://fedoraproject.org/wiki/EPEL](http://fedoraproject.org/wiki/EPEL)

4. [https://wiki.centos.org/TipsAndTricks/KickStart](https://wiki.centos.org/TipsAndTricks/KickStart)

5. [http://pykickstart.readthedocs.io/en/latest/](http://pykickstart.readthedocs.io/en/latest/)