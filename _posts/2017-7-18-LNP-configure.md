---
layout: post_layout
title: 简单编译安装nginx和php
time: 2017年7月18日 星期二
location: 昆明
pulished: true
excerpt_separator: "```"
---

### 前言

本次实验的平台为ubuntu-server-14.04

### 开始编译安装

+ 安装编译软件

```shell
$ sudo apt-get install gcc make
```

+ 编译安装nginx

```shell
//安装zlib依赖
$ wget https://nchc.dl.sourceforge.net/project/libpng/zlib/1.2.11/zlib-1.2.11.tar.gz
$ tar -xvf zlib-1.2.11.tar.gz
$ cd zlib-1.2.11/
$ ./configure --prefix=/usr/local/zlib/
$ make
$ sudo make install
//安装其他的依赖
$ sudo apt-get install libpcre3 libpcre3-dev openssl libssl-dev -y
//正式开始安装nginx
$ wget http://nginx.org/download/nginx-1.12.1.tar.gz
$ tar -xvf nginx-1.12.1.tar.gz
$ cd nginx-1.12.1/
$ ./configure --prefix=/usr/local/nginx/
$ make
$ sudo make install
```
+ 编译安装PHP

```shell
$ wget http://php.net/distributions/php-7.1.7.tar.bz2
$ tar -xvf php-7.1.7.tar.bz2
//安装依赖
$ sudo apt-get install -y libxml2 libxml2-dev
$ cd php-7.1.7/
$ ./configure --prefix=/usr/local/php/ --enable-fpm
$ make 
$ make test
$ sudo make install
```
