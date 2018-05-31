---
layout: post_layout
title: 小米路由器 mini
time: 2018年5月30日 星期三
location: 昆明
pulished: true
excerpt_separator: "```"
---

### 前言

历经千辛万苦终于把小米路由器mini成功刷上 `Padavan` ，在这里容我说句：“小米垃圾！！！”。真的恶心，升级高版本的固件之后，`SSH` 就打不开了，真你妈坑，在网上试了很多方法，最终刷了较早版本的开发版固件，然后进行了一次小升级，（对你没听错，我刷的固件稍微的旧了点，按照官方流程还是不能打开 `SSH`，当我打算放弃的时候，我又尝试了一次，最后的打开了 `SSH`）。当然爽歪歪了！

### 刷入不死 Breed

这步的前提是打开 `SSH`，大致操作参考官网，主要还是固件不要太新，这里主要参考了[小米路由器 mini 刷 Padavan 详细教程](https://blog.csdn.net/xhhjin/article/details/53458633)，固件也是用的作者提供的，当然你也可以在[这里下载（我把固件搬到了自己的服务器上）](https://files.skylens.co/xiaomimini/miwifi.bin)，直接使用手动方式安装固件（千万别用 U 盘，极有可能刷不进固件），然后更新系统（不用担心，这个版本较低，不会更新到最新的版本，但是更新完的版本可以输入 `SSH` 固件），更新完之后，按照正常步骤刷入 `SSH` 固件，下载好 [Breed](https://breed.hackpascal.net/breed-mt7620-xiaomi-mini.bin)然后登入系统，开始备份

先查看一下 `rom` (不知道是不是要核对一下)

```sh
cat /proc/mtd
dev:    size   erasesize  name
mtd0: 01000000 00010000 "ALL"
mtd1: 00030000 00010000 "Bootloader"
mtd2: 00010000 00010000 "Config"
mtd3: 00010000 00010000 "Factory"
mtd4: 00c80000 00010000 "OS1"
mtd5: 00b11e68 00010000 "rootfs"
mtd6: 00200000 00010000 "OS2"
mtd7: 00100000 00010000 "overlay"
mtd8: 00010000 00010000 "crash"
mtd9: 00010000 00010000 "reserved"
mtd10: 00010000 00010000 "Bdata"
```

开始备份

```sh
cd /tmp
mkdir rom
dd if=/dev/mtd0 of=/tmp/rom/ALL.bin
dd if=/dev/mtd1 of=/tmp/rom/Bootloader.bin
dd if=/dev/mtd2 of=/tmp/rom/Config.bin
dd if=/dev/mtd3 of=/tmp/rom/Factory.bin
dd if=/dev/mtd4 of=/tmp/rom/OS1.bin
dd if=/dev/mtd5 of=/tmp/rom/rootfs.bin
dd if=/dev/mtd6 of=/tmp/rom/OS2.bin
dd if=/dev/mtd7 of=/tmp/rom/overlay.bin
dd if=/dev/mtd8 of=/tmp/rom/crash.bin
dd if=/dev/mtd9 of=/tmp/rom/reserved.bin
dd if=/dev/mtd10 of=/tmp/rom/Bdata.bin
```

上传 `Breed` 至 `tmp` 目录， 开始刷入 `Breed`，刷完后路由器自动重启

```sh
cd /tmp
mtd -r write breed-mt7620-xiaomi-mini.bin Bootloader
```

重启完之后，插上网线检查 `PC` 是否分配了 `IP`，若能分配 `IP`，则证明能正常工作。按照如下流程进入 `Breed WEB 控制台`：

1、断开路由器的电源，用牙签等尖锐物按下路由器 `reset` 按钮后重新接入电源；

2、等到路由器的灯开始闪烁或 `ping` 通时即表明进入 `Breed WEB 控制台`，松开 `reset` 键

3、`PC` 访问 `192.168.1.1`，开到如下效果

<br>
<img src="/assets/post_pictures/bootloader.png" width="650">
&nbsp;
<br>

下载 `Padavan` 固件 [地址](http://opt.cn2qq.com/padavan/RT-AC54U-GPIO-30-xiaomimini-128M_3.4.3.9-099.trx)

### 输入 `Padavan` 固件

在启动方式里面选择的是__普通固件__，选择 `Padavan` 固件刷入搞定。自动重启完毕之后，浏览器输入 `192.168.123.1`，账号名和密码都是 `admin`，通过 `web` 界面完成进一步设置，`SSH` 用户名密码也是 `admin`。

### 其他

1. 安装 `opkg`             [参考](https://menyifan.com/2016/08/19/mini4/)

```sh
opkg.sh
```

2. `ss` 设置              [参考](http://blog.sina.com.cn/s/blog_4891cbc50102x460.html)

