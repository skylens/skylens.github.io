---
layout: post_layout
title: VMware vmnet0 未运行解决办法
time: 2017年12月24日 星期日
location: 昆明
pulished: true
excerpt_separator: "```"
---

### 前言

升级了 `VMware Workstion 14 pro` 之后，虚拟机网络桥接，一直提示 `vmnet0` 未运行

### 解决办法

1. 卸载网络适配器（VMnet1 和 VMnet8）

&nbsp;
<img src="/assets/post_pictures/delete_network_dev.png" width="300">
&nbsp;

2. 删除 VMware Workstation 中的所有网络

   `编辑 --> 虚拟网络编辑器 --> 更改设置 --> 移除网络 (所有的网络) --> 还原默认设置`

&nbsp;
<img src="/assets/post_pictures/VMware_network_success.png" width="600">
&nbsp;

