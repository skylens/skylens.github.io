---
layout: post_layout
title: virsh管理KVM虚拟机
time: 2017年5月27日 星期六
location: 昆明
pulished: true
excerpt_separator: "```"
---

### 介绍

`virsh`是一款kvm的命令行管理工具

### 用法及注意

```shell
# virsh list                //查看正在运行的虚拟机
# virsh list --all          //查看所有虚拟机
# virsh start vm1           //启动一个名为VM1的虚拟机
# virsh shutdown vm1        //关闭VM,需要在VM中安装acpid服务才可使用(默认最小化安装不可以)
# virsh destory vm1         //直接关闭VM的电源
# virsh suspend vm1         //挂起的VM，从远端的vnc连接是不会断开
# virsh resume vm1          //从挂起恢复的VM，vnc连接可以接着操作
# virsh define vm1          //定义虚拟机
# virsh undefine vm1        //取消虚拟机的定义
```