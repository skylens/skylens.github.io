---
layout: post_layout
title: 在Debian/ubuntu上搭建KVM
time: 2017年5月23日 星期二
location: 昆明
pulished: true
excerpt_separator: "```"
---

### 准备

```shell
$ egrep '(vmx|svm)'--color /proc/cpuinfo  //检查硬件是否支持虚拟化(有输出则证明支持)
$ lsmod | grep kvm  //检查内核模块是否加载
$ sudo modprobe kvm_intel  //Intel平台加载内核模块
$ sudo modprobe kvm_amd  //AMD平台加载内核模块
```

### 安装KVM

```shell
$ sudo apt-get install qemu-kvm qemu libvirt-bin
$ id [username]  //检查libvirtd用户组是否被创建，如果没有则执行下面的命令
$ sudo adduser [username] libvirtd  //ubuntu下添加libvirtd用户组
$ sudo adduser [username] libvirt-qemu  //debian下添加libvirtd用户组
$ execsu-l $USER  //重新载入更新后的组成员关系
```

### 配置桥接网络

```shell
$ sudo apt-get install bridge-utils
$ sudo brctl addbr br0
$ sudo vim /etc/network/interfaces  //作如下配置，注释掉eth0的配置，新建br0的配置
  #auto eth0
  #iface eth0 inet dhcp
  auto br0
  iface br0 inet dhcp
  bridge_ports eth0
  bridge_stp off
  bridge_fd 0
  bridge_maxwait 0
$ sudo/etc/init.d/networking restart  //重启网络服务
$ sudo ifconfig  //检查配置是否生效
```

### 初始磁盘镜像

```shell
$ qemu-img create -f qcow2 kvm_linux_test.qcow2 10G
```

### 用virt-install创建虚拟机

```shell
$ virt-install --name=kvm_linux_test\
--boot network,cdrom,menu=on\
--ram 2048\
--vcpus=2\
--disk path=~/kvm_linux_test.qcow2,size=10,format=qcow2,bus=virtio\
--cdrom=~/debian.iso\
--network bridge=br0,model=virtio\
--vnc --vncport=5900 --vnclisten=0.0.0.0\
--accelerate
```

### 用qemu-kvm创建虚拟机

```shell
$ sudo kvm -m 2048 -drive file=~/iso/windows_7_professional.iso,index=1,media=cdrom -drive file=~/image/win7_sp1_x64_en_vlk.qcow2,if=virtio -drive file=~/iso/virtio-win-0.1-74.iso,index=3,media=cdrom -net nic,model=virtio -net user -nographic -vnc :9 -usbdevice tablet  //windows
$ sudo kvm -m 1024 -cdrom debian.iso -drive file=kvm_linux_test.qcow2,if=virtio,index=0 -boot d -net nic -net user -nographic -vnc :1  //linux
//然后用vnc客户端连接，进行进一步安装
```

### 用xml文件创建虚拟机

```shell
$ 
```
### 参考

- [怎样在 Ubuntu 和 Debian 中通过命令行管理 KVM](http://www.linuxidc.com/Linux/2016-02/128731.htm)
- [基于Linux命令行KVM虚拟机的安装配置与基本使用](http://www.cnblogs.com/linuxprobe/p/5724546.html)
- [KVM 虚拟机命令行安装配置](http://blog.csdn.net/cd520yy/article/details/10000671)
- [openstack windows 镜像制作](http://cs2.swfc.edu.cn/~jsf/network/openstack_windows.html)
- [Creating qcow2 CentOS Image for OpenStack](https://www.ibm.com/developerworks/community/wikis/home?lang=en#!/wiki/OpenStack/page/Creating+qcow2+CentOS+Image+for+OpenStack)
- [OpenStack image guide](https://docs.openstack.org/image-guide/virt-install.html)
