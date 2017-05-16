---
layout: post_layout
title: Hadoop单节点部署
time: 2017年5月16日 星期二
location: 昆明
pulished: true
excerpt_separator: "```"
---

### 什么是Hadoop


### 在`Debian 8`上部署

1. 用户设置

```bash
$ sudo useradd -m hadoop -s /bin/bash
$ sudo passwd hadoop
$ sudo adduser hadoop sudo
```

2. 源设置

```bash
$ sudo nano /etc/apt/sources.list

deb http://mirrors.ustc.edu.cn/debian/ jessie main contrib non-free
deb http://mirrors.163.com/debian/ jessie main non-free contrib

$ sudo apt-get update
```

3. 安装软件

```bash
$ sudo apt-get install vim openssh-server openjdk-7-jre openjdk-7-jdk 
```

4. 设置Java环境变量

```bash
$ 
```

5. Hadoop安装