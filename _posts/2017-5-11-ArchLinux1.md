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

准备一个至少2GB的一个U盘，下载U盘启动制作工具，常见的U盘启动工具有软碟通(Windows平台下的工具)，rufus(windows平台下的开源绿色工具)，dd(*nux平台下的命令行工具)

如何使用**dd**制作启动U盘

```shell
$ sudo fdisk /dev/sdb   //格式化U盘
$ sudo dd bs=4M if=/home/skylens/Downloads/archlinux-2017.05.01-x86_64.iso of=/dev/sdb  //等待几分钟
```

### **安装**

- 网络

为了避免麻烦开机前直接插网线

- 分区(推荐至少分三个区)

```shell
# cfdisk
boot分区  /dev/sda1
swap分区  /dev/sda2
根分区    /dev/sda3
# lsblk  //检查分区
# mkfs.vfat -F32 /dev/sda1  //把boot分区格式化为FAT32格式
# mkswap /dev/sda2  //格式化swap分区
# swapon /dev/sda2  //激活swap分区
# mkfs.ext4 /dev/sda3  //把根分区格式化为ext4，当然也可以使用其他ArchLinux支持的格式
```

- 挂载分区

```shell
# mount /dev/sda3 /mnt  //把根分区挂载到临时的mnt目录下
# cd /mnt
# mkdir -p boot/efi  //在mnt目录下新建boot和efi目录
# mount /dev/sda1 /mnt/boot/efi  //把boot分区挂载到efi目录下
```

- 修改源

```shell
# grep -A 1 'China' /etc/pacman.d/mirrorlist > mirrorlist.bak //取出中国的源，避免操作失误先写到一个新文件中
# sed -r '/^\-/d' mirrorlist.bak > mirrorlist.bak && cat mirrorlist.bak1 //删除空行以'-'开头的行并检查
# cat mirrorlist.bak1 > /etc/pacman.d/mirrorlist  //替换原来的内容
# pacman -Sy  //更新一下源
```

- 安装基本系统

```shell
# pacstrap -i /mnt base base-devel
```

- 配置

```shell
# genfstab –p /mnt >> /mnt/etc/fstab  //生成fstab
# cat /mnt/etc/fstab  //检查生成的fstab是否正确
# arch-chroot /mnt  //转回到主目录
# nano /etc/locale.gen  //去掉en_US.UTF-8,zh_CN.GB18030,zh_CN.UTF-8前面 的注释
# locale-gen  //更新语言环境
# echo "LANG=en_US.UTF-8" > /etc/locale.conf  //设置默认locale
# ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime  //设置时区
# tzselect  //也可以用 tzselect 来设置时区
# hwclock --systohc --localtime  //windows双系统可设置为硬件时间，避免windows时间不正确
# echo XPS13 > /etc/hostname  //修改主机名
# passwd  //为root用户设置密码
# useradd -m -s /bin/bash skylens  //新建普通用户
# passwd skylens  //给普通用户设置密码
# mkinitcpio –p linux  //加载内核模块(这样解释不知道正不正确)
```

- 安装引导(主要是针对 UEFI 引导)

 - GRUB2

    ```shell
    # pacman -S grub-bios efibootmgr dosfstools os-prober   //os-prober双系统用户安装
    # grub-mkconfig –o /boot/grub/grub.cfg
    # grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=arch_grub --recheck
    ```

 - systemd-boot

    systemd-boot 默认安装了( PARTUUID 可以用 blkid 获得)

    ```shell
    # bootctl install
    # vim /boot/loader/entries/arch.conf
    title   Arch Linux
    linux   /vmlinuz-linux
    initrd  /initramfs-linux.img
    options root=PARTUUID=06d1a377-976d-47db-a907-9bf03bb8519b rootfstype=btrfs rw pcie_aspm=force i915.enable_rc6=7
    ```

- 安装结束重启

```shell
# exit
# umount -R /mnt
# reboot
```

- 基本设置

重启后，能进入tty终端，证明安装成功，接下来进行设置

```shell
# dhcpcd ens169
# systemctl enable dhcpcd
# pacman -S openssh net-tools vim sudo gksu htop
# systemctl enable dhcpcd
# systemctl enable sshd
```

- 安装各种驱动

```shell
# pacman -S xf86-video-intel xf86-input-synaptics xf86-input-elographics xf86-input-keyboard xf86-input-mouse xf86-input-libinput xf86-input-evdev
```

- 安装图形界面

```shell
# pacman -S xorg-server xorg-xinit wayland wayland-protocols gnome gnome-terminal i3 lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings
# systemctl enable lightdm
```

- 网络管理界面

```shell
# pacman -S network-manager-applet networkmanager networkmanager-dispatcher-ntpd networkmanager-openconnect networkmanager-openvpn networkmanager-pptp networkmanager-vpnc nm-connection-editor openvpn ppp pptpclient vpnc rp-pppoe wireless_tools wpa_supplicant
```

- 声音

```shell
# pacman -S alsa-utils pulseaudio gnome-alsamixer pavucontrol
```

- 安装并配置 `fcitx` 输入法

```shell
# pacman -S fcitx-im fcitx-sunpinyin fcitx-cloudpinyin fcitx-configtool
$ mkdir ~/.config/autostart/
$ cp /etc/xdg/autostart/fcitx-autostart.desktop ~/.config/autostart/
$ vim ~/.xprofile
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS=@im=fcitx
```

- 安装解压软件

```shell
# pacman -S p7zip file-roller unrar tar zip
```

- 安装其他软件

```shell
# pacman -S lxappearance nitrogen pcmanfm evince gedit arandr viewnior net-tools
```

- yaourt 设置

```shell
$ sudo vim /etc/pacman.conf   //添加如下内容

[archlinuxcn]
#The Chinese Arch Linux communities packages.
SigLevel = Optional TrustAll
Server   = https://mirrors.ustc.edu.cn/archlinuxcn/$arch

$ sudo pacman -Syu yaourt
```