---
layout: post_layout
title: youtube-dl使用技巧
time: 2017年5月19日 星期五
location: 昆明
pulished: true
excerpt_separator: "```"
---

### 介绍

[`youtube-dl`](https://rg3.github.io/youtube-dl/)是一款下载`youtube`视频的命令行小工具

### 下载安装

```shell
$ sudo curl -L https://yt-dl.org/downloads/latest/youtube-dl -o /usr/local/bin/youtube-dl
$ sudo chmod a+rx /usr/local/bin/youtube-dl
```

### 使用

**注意:`youtube-dl`在`fish shell`下效果不是很好，加了参数报错，建议使用`bash shell`**

**注意:`youtube-dl`有时是视频和音频分离的，需要而外的添加`ffmpeg`来辅助音视频的合并**

```shell
$ youtube-dl --proxy "socks5://127.0.0.1:1080" https://www.youtube.com/watch?v=xxxxx  //通过socks5协议来代理下载
$ youtube-dl --proxy "socks5://127.0.0.1:1080" -F https://www.youtube.com/watch?v=xxxxx  //加-F参数 列出可下载的格式及信息
$ youtube-dl --proxy "socks5://127.0.0.1:1080" -f 22 https://www.youtube.com/watch?v=xxxxx  //加-f 22参数 从上面的可下载的格式及信息中选择对应的format编号
```

+ **安装 `ffmpeg`**

    [windows版](https://ffmpeg.zeranoe.com/builds/) 下载可执行文件，并添加环境变量

    [debian版](https://www.ffmpeg.org/download.html#build-linux) 主要是 `debian` 和 `ubuntu` 版的

    [macOS版](https://www.ffmpeg.org/download.html#build-mac) 可直接使用 `brew` 安装

    [CentOS/RedHat版](https://rpmfusion.org/Configuration)

	```bash
	# yum localinstall --nogpgcheck https://download1.rpmfusion.org/free/el/rpmfusion-free-release-7.noarch.rpm https://download1.rpmfusion.org/nonfree/el/rpmfusion-nonfree-release-7.noarch.rpm
    ```
**1080P YouTube 视频下载方法**

```sh
$ youtube-dl -F https://www.youtube.com/watch?v=xxxxx
$ youtube-dl -f 138+251 https://www.youtube.com/watch?v=xxxxx    //138 和 251 分别对应 视频和音频，前提是安装了 ffmpeg
```
