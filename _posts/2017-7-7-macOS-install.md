---
layout: post_layout
title: macOS 启动盘制作
time: 2017年7月7日 星期五
location: 昆明
pulished: true
excerpt_separator: "```"
---

### 开始

准备一张8GB的U盘，到AppStore下载好macOS的镜像

### 格式化U盘

<img src="/assets/post_pictures/USB_Formate.png" width="650">
&nbsp;

### 打开终端

```shell
$ sudo /Applications/Install\ macOS\ Sierra.app/Contents/Resources/createinstallmedia --volume /Volumes/Sierra --applicationpath /Applications/Install\ macOS\ Sierra.app --nointeraction
```

输入密码，等待10多分钟完成