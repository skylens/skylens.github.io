---
layout: post_layout
title: 通过virt-install来创建虚拟机
time: 2017年5月24日 星期三
location: 昆明
pulished: true
excerpt_separator: "```"
---

### 开头

之前简单的介绍了一下在`在Debian/ubuntu上搭建KVM`，其中提到使用`virt-install`来创建虚拟机，接下来整理一下每个参数的含义

### 参数含义

- virt-install创建centos虚拟机

```shell
virt-install --virt-type kvm \
--name centos \
--ram 1024 \
--disk /tmp/centos.qcow2,format=qcow2 \
--network network=default \
--graphics vnc,listen=0.0.0.0 \
--noautoconsole \
--os-type=linux \
--os-variant=centos7.0 \
--location=/data/isos/CentOS-7-x86_64-NetInstall-1611.iso
```

- virt-install创建windows虚拟机

```shell
virt-install --connect qemu:///system \
--name ws2012 \
--ram 2048 \
--vcpus 2 \
--network network=default,model=virtio \
--disk path=ws2012.qcow2,format=qcow2,device=disk,bus=virtio \
--cdrom /path/to/en_windows_server_2012_x64_dvd.iso \
--disk path=/path/to/virtio-win-0.1-XX.iso,device=cdrom \
--vnc \
--os-type windows \
--os-variant win2k12 \
--os-distro windows \
--os-version 2012
```

### 解释

`--connect`  通过libvirt URI连接到虚拟机管理程序

`--virt-type`  要使用的管理程序名称(KVM,qemu,xen...)

`--name`  客户机实例名称

`--ram` 或 `--memory`  配置客户机内存分配(例如: --memory 512,maxmemory=1024)

`--vcpus`  配置客户机虚拟CPU(vcpu)数量

`--disk`  指定存储的各种选项(format是指存储的类型)

`--network`  配置客户机网络接口

`--graphics`  配置客户机显示设置

`--noautoconsole`  不要自动尝试连接到客户端控制台

`--os-type=`  在客户机上安装的操作系统类型

`--os-variant=`  在客户机上安装的操作系统

`--location=`  安装源设置

`--cdrom=`  光驱安装介质

