---
layout: post_layout
title: 使用 jenkins 部署通过 gogs 托管的 gitbook
time: 2018年12月3日 星期一
location: 昆明
pulished: true
excerpt_separator: "```"
---

### 前言

有这样一个需求，要部署一个私有的 gitbook 项目，gitbook 源码使用私有的 git 服务器 gogs 托管，每次推送到仓库之后，自动部署 gitbook 项目。首先查到的是 git hook ，但是怎么试，都不能成功。又重新寻找解决方案，然后知道了，自动化构建，自动化部署，最后找到了 jenkins 。使用 jenkins 自动部署 git 仓库中的被推送的 gitbook 代码。

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