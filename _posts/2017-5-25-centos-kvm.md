---
layout: post_layout
title: 搭建CentOS上的KVM实验环境
time: 2017年5月25日 星期四
location: 昆明
pulished: true
excerpt_separator: "```"
---

### 准备

使用VMware Workstations作为实验环境，安装CentOS7，启用虚拟化，分配2GB内存，20GB磁盘，只设置root用户密码，不添加普通用户

### 设置

**1.安装CentOS时，`SOFTWARE SELECTION`选择`Virtualization-Host`和`Virtualization-Platform`这两项，当然如果选择了最小安装，可以通过下面的命令来安装上面的软件**

```shell
# yum group install -y virtualization-hypervison
# yum group install -y virtualization-tools
# yum group install -y virtualization-platform
```

**2.对于如果想要图形管理界面，执行**

```shell
# yum group install -y virtualization-client
# yum group install -y gnome-desktop
```

**3.设置CentOS7的网络，默认CentOS网络不启动，启动网络**

```shell
# ifconfig -a
# dhclient ens33
```

**4.防火墙和SELinux设置**

```shell
# systemctl stop firewalld
# systemctl disable firewalld
# setenforce 0
# sed -i "s,SELINUX=enforcing,SELINUX=disabled,g" /etc/sysconfig/selinux
```

**5.安装设置VNC服务器**

```shell
# yum install -y tighter*
# vim /etc/libvirt/qemu.conf
vnc_listen = "0.0.0.0"  //取消前面的注释
# systemctl restart libvirtd
```

**6.建立数据目录**

```shell
# mkdir -p /data/{isos,images}
```