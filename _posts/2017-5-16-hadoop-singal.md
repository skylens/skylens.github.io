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

**1.用户设置**

```shell
$ sudo useradd -m hadoop -s /bin/bash
$ sudo passwd hadoop
$ sudo adduser hadoop sudo
$ sudo su - hadoop
```

**2.源设置**

```shell
$ sudo nano /etc/apt/sources.list

deb http://mirrors.ustc.edu.cn/debian/ jessie main contrib non-free
deb http://mirrors.163.com/debian/ jessie main non-free contrib

$ sudo apt-get update
```

**3.安装软件**

```shell
$ sudo apt-get install vim openssh-server openjdk-7-jre openjdk-7-jdk
```

**4.设置Java环境变量**

```shell
$ vim ~/.bashrc  //最前面添加
export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64

$ source ~/.bashrc
$ $JAVA_HOME/bin/java -version
```

**5.设置免密码登录**

```shell
$ ssh-keygen -t rsa
$ ssh-copy-id hadoop@localhost
$ ssh localhost
```

**6.下载安装Hadoop**

```shell
$ wegt http://mirror.bit.edu.cn/apache/hadoop/common/hadoop-2.7.1/hadoop-2.7.1.tar.gz
$ sudo tar -zxvf hadoop-2.7.1.tar.gz -C /usr/local
$ cd /usr/local
$ sudo mv hadoop-2.7.1 hadoop
$ sudo chown -R hadoop ./hadoop
```

**7.配置Hadoop**

```shell
$ cd /usr/local/hadoop
$ mkdir ./input
$ cp ./etc/hadoop/*.xml ./input
$ ./bin/hadoop jar ./share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar grep ./input ./output 'dfs[a-z.]+'
$ cat ./output/*
```