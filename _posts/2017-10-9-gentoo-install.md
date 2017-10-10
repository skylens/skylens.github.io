---
layout: post_layout
title: uefi 安装 gentoo
time: 2017年10月9日 星期一
location: 昆明
pulished: true
excerpt_separator: "```"
---

### 前言

  Gentoo 是一款基于 Linux 的自由操作系统，它能为几乎任何应用程序或需求自动地作出优化和定制。 Gentoo的哲学是自由和选择。Gentoo 拥有媲美 FreeBSD 的广受美誉的 ports 系统 —— Portage 包管理系统。不同于 APT 和 YUM 等二进制文件分发的包管理系统，Portage 是基于源代码分发的，必须编译后才能运行，对于大型软 件而言比较慢，不过正因为所有软件都是在本地机器编译的，在经过各种定制的编译参数优化后，能将机器的硬件性能发挥到极致。Gentoo 是所有 Linux 发行版本里安装最复杂的，但是又是安装完成后最便于管理的版本，也是在相同硬件环境下运行最快的版本。

  怀着好奇心在虚拟机上尝试了一下 Gentoo 的安装，并针自己的情况做了一些优化，最后把整个过程记录了下来

  比如说：

  + UEFI/GPT 方式安装 
  + 源的修改（可以使用[163的源](http://mirrors.163.com/.help/gentoo.html)， 也可以使用[中科大的源](http://mirrors.ustc.edu.cn/help/gentoo.html)，我是用的是中科大的源）
  + 守护进程管理工具是用 systemd ，而没有用自带的 OpenRC （因为用习惯了 systemd ），所以 [stage3 要选择 systemd 版的](http://mirrors.ustc.edu.cn/gentoo/releases/amd64/autobuilds/current-stage3-amd64-systemd/)
  + 语言使用中文
  + 内核使用的是 [ck-sources](https://packages.gentoo.org/packages/sys-kernel/ck-sources)
  + ...

### 准备

  **注意 : Gentoo 的最小安装镜像不支持 UEFI，他只支持传统的 MBR ；如果你想使用 UEFI，那么就要选择基于 Gentoo 且支持 UEFI 启动的镜像（比如 [live DVD](http://mirrors.ustc.edu.cn/gentoo/releases/amd64/) 或者 [SystemRescueCD](http://www.system-rescue-cd.org/Download/) ）来安装，当然了你也可以使用支持 UEFI 启动的镜像（如 [Archlinux 的镜像](http://mirrors.ustc.edu.cn/archlinux/iso/)）制作 U 盘启动盘，通过启动盘来安装 Gentoo ！**

  制作 U 盘启动盘

  常用工具有[rufus](http://rufus.akeo.ie/?locale=zh_CN) dd、[软碟通](https://cn.ultraiso.net/)以及其它你习惯的工具。

### 安装

- 安装前准备

  启用 SSH 设置 root 用户的密码（这样就可以用其他 PC 来远程完成安装工作了）

  ```shell
  # systemctl start sshd
  # passwd
  # ping -c 3 163.com
  ``` 

- 分区

<br>
<img src="/assets/post_pictures/partion.png" width="650">
&nbsp;
<br>

- 格式化分区

  主分区一般默认使用 ext4 格式，boot 分区一般使用 vfat 格式

  主分区可以选择其他的文件系统，但是要安装[对应的包](https://wiki.gentoo.org/wiki/Handbook:AMD64/Full/Installation#Applying a filesystem to a partition)

  ```shell
  # mkfs.ext4 /dev/sda3
  # mkswap /dev/sda2 && swapon /dev/sda2
  # mkfs.fat -F 32 /dev/sda1
  ```

- 挂载主分区及必要的文件系统

  ```shell
  # mkdir -p /mnt/gentoo
  # mount /dev/sda3 /mnt/gentoo
  # mount -t proc /proc /mnt/gentoo/proc
  # mount --rbind /sys /mnt/gentoo/sys
  # mount --make-rslave /mnt/gentoo/sys
  # mount --rbind /dev /mnt/gentoo/dev
  # mount --make-rslave /mnt/gentoo/dev
  ```

- 时间同步

  ```shell
  # date
  # date 100313162016  设置时间为 2016年10月3号13点16分 
  ```

- 下载并解压 stage3 

  ```shell
  # wget https://mirrors.ustc.edu.cn/gentoo/releases/amd64/autobuilds/current-stage3-amd64-systemd/stage3-amd64-systemd-20171003.tar.bz2
  # tar xvjpf stage3-*.tar.bz2 --xattrs --numeric-owner
  ```

- 修改 make.conf 文件

  ```shell
  # nano -w /mnt/gentoo/etc/portage/make.conf   添加
  MAKEOPTS="-j8"
  GENTOO_MIRRORS="https://mirrors.ustc.edu.cn/gentoo/"
  ```

- 设置 Gentoo ebuild repository

  ```shell
  # mkdir /mnt/gentoo/etc/portage/repos.conf
  # nano -w /mnt/gentoo/etc/portage/repos.conf/gentoo.conf   添加
  [gentoo]
  location = /usr/portage
  sync-type = rsync
  sync-uri = rsync://mirrors.ustc.edu.cn/gentoo-portage
  auto-sync = yes
  ```

- 复制 DNS 配置文件

  ```shell
  # cp -L /etc/resolv.conf /mnt/gentoo/etc/
  ```

- 挂载 boot 分区

  ```shell
  # mount -v /dev/sda1 /mnt/gentoo/boot
  ```

- 设置新的环境

  ```shell
  # chroot /mnt/gentoo /bin/bash
  # source /etc/profile
  # export PS1="(chroot) $PS1"
  ```

- 安装并更新 ebuild repository

  ```shell
  # emerge-webrsync
  # emerge --sync
  ```

- 选择 profile 

```shell
# eselect profile list
  [1]   default/linux/amd64/13.0
  [2]   default/linux/amd64/13.0/selinux
  [3]   default/linux/amd64/13.0/desktop
  [4]   default/linux/amd64/13.0/desktop/gnome
  [5]   default/linux/amd64/13.0/desktop/gnome/systemd
  [6]   default/linux/amd64/13.0/desktop/plasma
  [7]   default/linux/amd64/13.0/desktop/plasma/systemd
  [8]   default/linux/amd64/13.0/developer
  [9]   default/linux/amd64/13.0/no-multilib
  [10]  default/linux/amd64/13.0/systemd *
  [11]  default/linux/amd64/13.0/x32
  [12]  hardened/linux/amd64
  [13]  hardened/linux/amd64/selinux
  [14]  hardened/linux/amd64/no-multilib
  [15]  hardened/linux/amd64/no-multilib/selinux
  [16]  hardened/linux/amd64/x32
  [17]  hardened/linux/musl/amd64
  [18]  hardened/linux/musl/amd64/x32
  [19]  default/linux/uclibc/amd64
  [20]  hardened/linux/uclibc/amd64
# eselect profile set 10
```

- 更新@world集合

```shell
# emerge --ask --update --deep --newuse @world
```

- 时区设置

```shell
# echo "Asia/Shanghai" > /etc/timezone 
# emerge --config sys-libs/timezone-data
```

- 语言设置

```shell
# nano -w /etc/locale.gen   添加
en_US.UTF-8 UTF-8
zh_CN.UTF-8 UTF-8
zh_CN.GBK GBK
zh_CN.GB2312 GB2312
zh_CN.GB18030 GB18030
# local-gen
# eselect locale list
  [1]   C
  [2]   en_US.utf8
  [3]   POSIX
  [4]   zh_CN.gb18030
  [5]   zh_CN.gb2312
  [6]   zh_CN.gbk
  [7]   zh_CN.utf8
  [ ]   (free form)
# eselect locale set 7
```

- 重新加载新的环境

```shell
# env-update && source /etc/profile && export PS1="(chroot) $PS1"
```

- 选择内核

1.使用 ck-sources 内核

```shell
# echo sys-kernel/ck-sources ~amd64 >> /etc/portage/package.accept_keywords
# emerge --ask sys-kernel/ck-sources
# emerge --ask sys-apps/pciutils
# emerge --ask sys-kernel/genkernel
```

2.使用 gentoo-sources

```shell
# emerge --ask sys-kernel/gentoo-sources
# emerge --ask sys-kernel/genkernel
```

- 编译内核

1.完全编译

```shell
# cd /usr/src/linux
# genkernel --menuconfig all
```

2.根据自己的需求编译

```shell
# cd /usr/src/linux
# make menuconfig
# make -j8 && make modules_install
# make install
# genkernel --install initramfs
```

- 检查

```shell
# ls /boot/kernel* /boot/initramfs*
```

- 安装固件

```shell
# emerge --ask sys-kernel/linux-firmware
```

- 配置 fstab 文件

```shell
# blkid
# nano -w /etc/fstab   追加
/dev/sda1                                       /boot   vfat    noatime                                         1 2
UUID="df97107b-9ebe-469e-b4a3-8819b110415a"     /       ext4    discard,noatime,commit=600,errors=remount-ro    0 1
```

- 安装必要的软件

```shell
# emerge --autounmask-write networkmanager
# etc-update --automode -3
# emerge --ask networkmanager sys-apps/pcmciautils app-admin/sysklogd sys-process/cronie sudo layman net-misc/ntp
```

- 简单的设置

```shell
# nano -w /etc/suders   取消注释
%wheel ALL=(ALL) ALL
# echo "root:1" | chpasswd
# useradd -m -G users,wheel,audio,cdrom,usb,video,portage -s /bin/bash skylens
# echo "skylens:1" | chpasswd
```

- 安装 Grub2 

```shell
# echo 'GRUB_PLATFORMS="efi-64"' >> /etc/portage/make.conf
# emerge --ask sys-boot/grub:2
# grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=Gentoo
```

- 启用 systemd

```shell
# nano -w /etc/default/grub   取消注释 
GRUB_CMDLINE_LINUX="init=/usr/lib/systemd/systemd"
# ln -sf /proc/self/mounts /etc/mtab
# systemd-machine-id-setup
# systemctl enable NetworkManager
```

- 生成 grub 配置文件

```shell
# grub-mkconfig -o /boot/grub/grub.cfg
```

- 收尾（卸载分区及其他文件系统，重启）

```shell
# exit
# cd
# umount -l /mnt/gentoo/dev{/shm,/pts,}
# umount -R /mnt/gentoo
# reboot
```