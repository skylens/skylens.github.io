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

- Windows Server 2012 r2 镜像地址

`ed2k://|file|cn_windows_server_2012_r2_with_update_x64_dvd_6052725.iso|5545705472|121EC13B53882E501C1438237E70810D|/`

- 文件存放位置

```shell
# mkdir /openstack-image
# chown -R qemu:qemu /openstack-image
# cd /openstack-image
```

- 驱动及 CloudbaseInit

```shell
# wget https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/archive-virtio/virtio-win-0.1.126-2/virtio-win-0.1.126.iso
# mkdir CloudbaseInitSetup/ && cd CloudbaseInitSetup/
# wget https://cloudbase.it/downloads/CloudbaseInitSetup_0_9_11_x64.msi 
# cd ../
# mkisofs -o CloudbaseInitSetup.iso CloudbaseInitSetup/
```

+ 创建磁盘

```shell
# qemu-img create -f qcow2 ws2012r2.qcow2 20G
```

+ 启动安装

- 使用 virt-install 安装

```shell
virt-install --name=ws2012r2 --ram=2048 --cpu=host --vcpus=2 \
--os-type=windows --os-variant=win2k12r2 \
--disk ws2012r2.qcow2,bus=virtio \
--disk cn_windows_server_2012_r2_datacenter_with_update_x64_dvd_4048415.iso,device=cdrom,bus=ide \
--disk virtio-win-0.1.126.iso,device=cdrom,bus=ide \
--disk CloudbaseInitSetup_0.9.11_x64.iso,device=cdrom,bus=ide \
--network network=default,model=virtio \
--graphics vnc,listen=0.0.0.0 --noautoconsole
```

- 使用 qemu-kvm 安装

```shell
qemu-kvm -m 2048 -drive file=ws2012r2.qcow2,if=virtio,index=0,media=disk \
-drive file=cn_windows_server_2012_r2_datacenter_with_update_x64_dvd_4048415.iso,index=1,media=cdrom \
-drive file=virtio-win-0.1.126.iso,index=2,media=cdrom \
-drive file=CloudbaseInitSetup_0.9.11_x64.iso,index=3,media=cdrom \
-net nic,model=virtio -net user -boot d -nographic -vnc 0.0.0.0:0
```

+ 使用 VNC 完成安装

`netstat -ntlp | grep qemu-kvm` 查看 VNC 的端口

+ 添加硬盘驱动

+ 安装网卡驱动

+ 安装 CloudbaseInit

+ 压缩镜像

```shell
# virt-sparsify --compress ./ws2012r2.qcow2 MS2012R2.qcow2
```

### 参考

[制作openstack的windows server 2012r2镜像](http://www.jianshu.com/p/c92c3c9a2d6f)