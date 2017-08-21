---
layout: post_layout
title: 在 CentOS 下制作 Windows Server 2012 R2 镜像
time: 2017年8月21日 星期一
location: 昆明
pulished: true
excerpt_separator: "```"
---

### 前言

在 CentOS 下制作 OpenStack 的 Windows Server 2012 R2 的虚拟机镜像

### 准备

+ 安装软件并启动服务

```shell
$ sudo su - root
# yum groupinstall Virtualization "Virtualization Client"
# yum install libvirt
# systemctl enable libvirtd
# systemctl start libvirtd
# systemctl status libvirtd
```

+ 准备镜像

Windows Server 2012 r2 镜像地址

`ed2k://|file|cn_windows_server_2012_r2_with_update_x64_dvd_6052725.iso|5545705472|121EC13B53882E501C1438237E70810D|/`

驱动及 CloudbaseInit

```shell
# wget https://fedorapeople.org/groups/virt/virtio-win/deprecated-isos/stable/virtio-win-0.1-81.iso
# mkdir CloudbaseInitSetup/ && cd CloudbaseInitSetup/
# wget https://cloudbase.it/downloads/CloudbaseInitSetup_0_9_11_x64.msi 
# cd ../
# mkisofs -o CloudbaseInitSetup.iso CloudbaseInitSetup/
```

文件存放位置

```shell
# mkdir /openstack-image
# chown -R qemu:qemu /openstack-image
```

### 参考

[制作openstack的windows server 2012r2镜像](http://www.jianshu.com/p/c92c3c9a2d6f)