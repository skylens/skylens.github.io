---
layout: post_layout
title: 编译安装 shadowsocks-libev
time: 2017年8月4日 星期五
location: 昆明
pulished: true
excerpt_separator: "```"
---

### 前言

在 Raspberry Pi 3 上编译安装 shadowsocks-libev ，实现透明代理

### 准备

+ 下载

1. 下载最新的 shadowsocks-libev 版本

```bash
#!/bin/bash
ver=$(wget --no-check-certificate -qO- https://api.github.com/repos/shadowsocks/shadowsocks-libev/releases/latest | grep 'tag_name' | cut -d\" -f4)
shadowsocks_libev_ver="shadowsocks-libev-$(echo ${ver} | sed -e 's/^[a-zA-Z]//g')"
download_link="https://github.com/shadowsocks/shadowsocks-libev/releases/download/${ver}/${shadowsocks_libev_ver}.tar.gz"
wget $download_link
``` 

2. 下载加密模块

```shell
$ wget https://github.com/jedisct1/libsodium/releases/download/1.0.13/libsodium-1.0.13.tar.gz 
$ wget http://dl.teddysun.com/files/mbedtls-2.5.1-gpl.tgz
```

+ 安装依赖

```shell
$ sudo su
# apt-get -y --no-install-recommends install gettext build-essential autoconf automake libtool openssl libssl-dev zlib1g-dev libpcre3-dev libudns-dev libev-dev
# tar -xvf libsodium-1.0.13.tar.gz && cd libsodium-1.0.13
# ./configure --prefix=/usr && make && make install
# tar -xvf mbedtls-2.5.1-gpl.tgz && cd mbedtls-2.5.1
# make SHARED=1 CFLAGS=-fPIC
# make DESTDIR=/usr install
# ldconfig
```

+ 编译安装 shadowsocks-libev

```shell
# tar -xvf shadowsocks-libev-3.0.8.tar.gz && cd shadowsocks-libev-3.0.8
# ./configure --disable-documentation
# make && make install
# cp debian/config.json /etc/shadowsocks-libev/
# vim /etc/shadowsocks-libev/config.json
```

+ 启动服务

```shell
$ sudo ss-local -c /etc/shadowsocks-libev/config.josn -u
$ sudo ss-tunnel -c /etc/shadowsocks-libev/config.josn -L 8.8.8.8:53 -u
```

### 尾巴

终于写完了，Raspberry Pi 3 上编译安装巨慢，花了我整整一个晚上 ，累死了，先写到这过两天再完善 :(

### 链接

[shadowsocks-libev 安装脚本](https://github.com/teddysun/shadowsocks_install/blob/master/shadowsocks-libev-debian.sh)

[libsodium 安装](https://download.libsodium.org/doc/installation/index.html#integrity-checking)

[不编译shadowsocks的文档](https://lixingcong.github.io/2016/07/20/compile-with-no-doc-for-shadowsocks/)
