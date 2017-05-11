---
layout: post_layout
title: ArchLinux折腾日记:安装篇
time: 2017年5月11日 星期四
location: 昆明
pulished: true
excerpt_separator: "```"
---

### **ArchLinux是什么**

Arch Linux 是通用 i686/x86-64 GNU/Linux 发行版。Arch采用滚动升级模式，尽全力提供最新的稳定版软件。初始安装的Arch只是一个基本系统，随后用户可以根据自己的喜好安装需要的软件并配置成符合自己理想的系统。

### **制作ArchLinux启动U盘**

准备一个至少2GB的一个U盘，下载U盘启动制作工具，常见的U盘启动工具有软碟通(Windows平台下的工具)，rufus(windows平台下的开源绿色工具)，dd(*ux平台下的命令行工具)

如何使用**dd**制作启动U盘

{% highlight bash %}
$ sudo fdisk /dev/sdb   //格式化U盘
$ sudo dd bs=4M if=/home/skylens/Downloads/archlinux-2017.05.01-x86_64.iso of=/dev/sdb  //等待几分钟
{% endhighlight %}

### **安装**

- 网络

为了避免麻烦开机前直接插网线

- 分区(推荐至少分三个区)

{% highlight bash %}
# cfdisk  
boot分区  /dev/sda1
swap分区  /dev/sda2
根分区    /dev/sda3
# lsblk  //检查分区
# mkfs.vfat -F32 /dev/sda1  //把boot分区格式化为FAT32格式
# mkswap /dev/sda2  //格式化swap分区
# swapon /dev/sda2  //激活swap分区
# mkfs.ext4 /dev/sda3  //把根分区格式化为ext4，当然也可以使用其他ArchLinux支持的格式
{% endhighlight %}

- 挂载分区

{% highlight bash %}
# mount /dev/sda3 /mnt  //把根分区挂载到临时的mnt目录下
# cd /mnt
# mkdir -p boot/efi  //在mnt目录下新建boot和efi目录
# mount /dev/sda1 /mnt/boot/efi  //把boot分区挂载到efi目录下
{% endhighlight %}


- 修改源

{% highlight bash %}
# grep -A 1 'China' /etc/pacman.d/mirrorlist > mirrorlist.bak //取出中国的源，避免操作失误先写到一个新文件中
# vi /etc/pacman.d/mirrorlist  //检查，删除空行
# cat mirrorlist.bak > /etc/pacman.d/mirrorlist  //替换原来的内容
# pacman -Sy  //更新一下源
{% endhighlight %}

- 安装基本系统

{% highlight bash %}
# pacstrap -i /mnt base base-devel
{% endhighlight %}

- 配置

{% highlight bash %}
# genfstab –p /mnt >> /mnt/etc/fstab  //生成fstab
# cat /mnt/etc/fstab  //检查生成的fstab是否正确
# arch-chroot /mnt  //转回到主目录
# nano /etc/locale.gen  //去掉en_US.UTF-8,zh_CN.GB18030,zh_CN.UTF-8前面 的注释
# locale-gen  //更新语言环境
# echo "LANG=en_US.UTF-8" > /etc/locale.conf  //设置默认locale
# ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime  //设置时区
# hwclock --systohc --localtime  //windows双系统可设置为硬件时间，避免windows时间不正确
# echo XPS13 > /etc/hostname  //修改主机名
# passwd  //为root用户设置密码
# useradd -m -s /bin/bash skylens  //新建普通用户
# passwd skylens  //给普通用户设置密码
# mkinitcpio –p linux  //加载内核模块(这样解释不知道正不正确)
{% endhighlight %}

- 安装grub

{% highlight bash %}
# pacman -S grub-bios efibootmgr dosfstools os-prober   //os-prober双系统用户安装
# grub-mkconfig –o /boot/grub/grub.cfg
# grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=arch_grub --recheck 
{% endhighlight %}

- 结尾

{% highlight bash %}
# exit
# umount -R /mnt
# reboot
{% endhighlight %}

至此，系统已经基本安装完成了，重启后就能进入黑乎乎的界面了，图形界面还需要自己选择安装！
