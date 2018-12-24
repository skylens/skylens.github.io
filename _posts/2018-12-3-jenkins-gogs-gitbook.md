---
layout: post_layout
title: 使用 jenkins 部署通过 gogs 托管的 gitbook
time: 2018年12月3日 星期一
location: 昆明
pulished: true
excerpt_separator: "```"
---

### 前言

个人在 VPS 上使用 gogs 部署了一个私有的 git 代码托管平台。gitbook 作为一款高颜值的开源的文档管理工具。最近 gitbook 官方托管的文档服务老是从 github同步不了，于是想使用 gogs 结合 gitbook 搭建一个私有的的文档管理系统，记录一些自己平时的笔记。使用关键词从搜索引擎中查找蛛丝马迹，最终确定了使用 jekins 来搭建一套半自动化的文档管理系统。

### 部署 gogs

### 安装 gitbook

+ 安装 nodejs

```sh
curl -sL https://rpm.nodesource.com/setup_8.x | bash -
yum install -y nodejs
```

+ 安装 gitbook-cli

```sh
npm install gitbook-cli -g
```

### 安装 jdk

是的， jenkins 是基于 java 的！

```sh
mkdir /opt/java
tar -xvf jdk-8u191-linux-x64.tar.gz -C /opt/java
mv /opt/java/jdk1.8.0_191 /opt/java/jdk

cat >/etc/profile.d/jdk.sh<<EOF
#!/bin/sh

JAVA_HOME=/opt/java/jdk
CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
PATH=$JAVA_HOME/bin:$PATH
EOF

chmod +x /etc/profile.d/jdk.sh
. /etc/profile
```

### 安装 jenkins

```sh
mkdir /opt/jenkins && cd /opt/jenkins
wget http://mirrors.jenkins.io/war-stable/latest/jenkins.war
chown -R www-data.www-data /opt/jenkins
su - www-data
tmux
java -jar jenkins.war --httpPort=8080
```

浏览器访问 http://IP地址:8080 ，进行进一步的安装配置

**一定要安装插件 `Generic Webhook Trigger Plugin`**

生成 token

```sh
openssl rand -hex 12
30910262eff664f2acbcb962
```

- 在 gogs 仓库对应的 `管理 web 钩子` 处设置

推送地址：http://IP地址:8080/generic-webhook-trigger/invoke?token=30910262eff664f2acbcb962

数据格式：设置为application/json

密钥文本：为空

请设置您希望触发Web钩子的事件：推送

是否激活：勾选
