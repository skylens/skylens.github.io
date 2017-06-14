---
layout: post_layout
title: 怎样在Linux下录屏
time: 2017年6月14日 星期三
location: 昆明
pulished: true
excerpt_separator: "```"
---

### [Open Broadcaster Software](https://obsproject.com/) (简称OBS)

OBS是一款开源免费的录屏及视频直播软件。OBS不仅有Linux版的也支持Window、macOS，是一款非常优秀的录屏软件。

- [安装](https://github.com/jp9000/obs-studio/wiki/Install-Instructions#linux)

```shell
$ sudo pacman -S obs-studio  //ArchLinux/Manjaro
```

- 配置

可使用图形界面配置，配置相对比较复杂。

### [SimpleScreenRecorder](http://www.maartenbaert.be/simplescreenrecorder/) (简称SSR)

SSR是一款开源的Linux屏幕录制软件，使用基于 Qt 的图形用户界面开发。可录制整个屏幕或者部分屏幕，或者直接录制 OpenGL 应用，录制过程中允许暂停和恢复，支持多种文件格式和编码。

- [安装](http://www.maartenbaert.be/simplescreenrecorder/#download)

```shell
$ sudo pacman -S simplescreenrecorder  //ArchLinx/Manjaro
```

- 配置

有图形界面，配置相对OBS较简单

### [recordMyDesktop](http://recordmydesktop.sourceforge.net/about.php)

- 安装

```shell
$ sudo pacman -S recordmydesktop  //ArchLinux/Manjaro
// 图形界面可选安装
$ sudo pacman -S gtk-recordmydesktop  //gtk图形界面
$ sudo pacman -S qt-recordmydesktop   //qt图形界面
```

- 配置

配置超级简单，不安装图形界面，一条命令实现录屏，但是每次停止都要转码，速度慢

```shell
$ recordmydesktop --no-cursor --fps 30 --no-sound --no-wm-check --no-frame   //开始录屏，Ctrl+C 停止
```

### FFmpeg

FFmpeg是一套可以用来记录、转换数字音频、视频，并能将其转化为流的开源计算机程序。采用LGPL或GPL许可证。它提供了录制、转换以及流化音视频的完整解决方案。

- [安装](https://wiki.archlinux.org/index.php/FFmpeg#Package_installation)

一般Linux的发型版都自带了

```shell
$ sudo pacman -S ffmpeg  //ArchLinux/Manjaro
```

- [配置](https://wiki.archlinux.org/index.php/FFmpeg#Screen_cast)

ffmpeg配置超级简单和recordMyDesktop一样，一条命令就可以实现录屏，Ctrl+C 停止

```shell
$ ffmpeg -video_size 1920x1080 -framerate 30 -f x11grab -i :0.0 -c:v libx264 -qp 0 -preset ultrafast ~/Videos/screen.mkv
```
