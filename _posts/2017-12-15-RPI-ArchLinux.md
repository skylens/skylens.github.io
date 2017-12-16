---
layout: post_layout
title: Raspberry Pi 3 上安装 Arch Linux
time: 2017年12月15日 星期五
location: 昆明
pulished: true
excerpt_separator: "```"
---

### 前言

在 Raspberry Pi 3 上安装 Arch Linux ARM 版

### 准备

Linux 操作系统（任何 Linux 发行版都行）、Raspberry Pi 3 对应的 Arch Linux ARM 版镜像、SD卡

镜像下载地址（三选一）

官方 `http://os.archlinuxarm.org/os/ArchLinuxARM-rpi-2-latest.tar.gz`

中科大源 `http://mirrors.ustc.edu.cn/archlinuxarm/os/ArchLinuxARM-rpi-2-latest.tar.gz`

清华大学源 `https://mirrors.tuna.tsinghua.edu.cn/archlinuxarm/os/ArchLinuxARM-rpi-2-latest.tar.gz`

### 分区及格式化分区

**假设我的 SD 卡叫 sdb**

可以用 Linux 各种分区工具（fdisk、cfdisk、gparted、parted 等等）

```bash
parted -s -a optimal -- /dev/sdb mklabel msdos
parted -s -a optimal -- /dev/sdb unit mib mkpart primary 1 101 name 1 boot
parted -s -a optimal -- /dev/sda unit mib mkpart primary 101 -1 name 2 rootfs
```

格式化

```bash
mkfs.vfat /dev/sdb1
mkfs.ext4 /dev/sdb2
```

### 挂载分区

```bash
mkdir root boot
mount /dev/sdb1 boot
mount /dev/sdb2 root
```

### 解压安装系统

```bash
bsdtar -xpf ArchLinuxARM-rpi-2-latest.tar.gz -C root
sync
mv root/boot/* boot
umount boot root
```

### 启动树梅派

### 设置 `SWAP`

```bash
fallocate -l 1024M /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
echo 'vm.swappiness=1' > /etc/sysctl.d/99-sysctl.conf
echo '/swapfile       none    swap    defaults        0       0' >> /etc/fstab
```

### 软件源

```bash
nano /etc/pacman.d/mirrorlist

Server = https://mirrors.ustc.edu.cn/archlinuxarm/$arch/$repo
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxarm/$arch/$repo

pacman -Sy pacman
pacman-key --init
pacman -S archlinux-keyring
pacman-key --populate archlinux
pacman -Syu --ignore filesystem
pacman -S filesystem --force

reboot
```

### 时间及时区

```bash
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
pacman -S ntp
systemctl enable ntpd.service
systemctl start ntpd.service
```

### 软件

```bash
pacman -S base-devel sudo vim htop libnewt dialog wpa_supplicant wireless_tools iw
wget https://aur.archlinux.org/cgit/aur.git/snapshot/neofetch.tar.gz
tar -xvf wget neofetch.tar.gz
cd neofetch
makepkg -si
```

### 参考

1. [**`archlinuxarm`**](https://archlinuxarm.org/platforms/armv8/broadcom/raspberry-pi-3#installation)

2. [**`Raspberry-Pi-Setup-Guide`**](https://github.com/phortx/Raspberry-Pi-Setup-Guide)

3. [**`installing-arch-linux-raspberry-pi`**](https://www.novaspirit.com/2017/04/25/installing-arch-linux-raspberry-pi/)

4. [**`offline_installation_of_packages`**](https://wiki.archlinux.org/index.php/offline_installation_of_packages)
