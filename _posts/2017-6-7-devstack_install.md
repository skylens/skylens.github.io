---
layout: post_layout
title: DevStack的安装
time: 2017年6月7日 星期三
location: 昆明
pulished: true
excerpt_separator: "```"
---

### DevStack

DevStack是一种OpenStack的安装方式，采用依靠一系列脚本对OpenStack完成安装

### 安装

- 实验环境

ubuntu server 16.04 (4GB RAM + 60GB 磁盘) + OpenStack newton版 进行验证安装

- 系统设置

```shell
$ sudo vim /etc/network/interface
auto eth0
address 192.168.1.123
netmask 255.255.255.0
gateway 192.168.1.254
dns-nameservers 114.114.114.114

$ sudo /etc/init.d/networking restart
$ sudo vim /etc/ssh/sshd_config
$ sudo /etc/init.d/ssh restart
$ cd /etc/apt/
$ sudo cp source.list source.list.bak
$ sudo vim source.list
$ sudo apt-get update
$ sudo apt-get install git sudo python-pip -y
```

- 安装DevStack

```shell
$ sudo su - root
# ntpdate time.windows.com
# mkdir .pip && cd .pip
# vim pip.conf
[global]
timeout = 6000
index-url = http://pypi.douban.com/simple/
[install]
trusted-host = pypi.douban.com
# cd /home
# git clone http://git.trystack.cn/openstack-dev/devstack.git -b stable/newton
# cd devstack/tools/
# ./create-stack-user.sh
# chown -R stack:stack /home/devstack
# chmod 777 /dev/pts/0
# su stack
$ cd /home/devstack
$ vim local.conf

[[local|localrc]]

# use TryStack git mirror
GIT_BASE=http://git.trystack.cn
NOVNC_REPO=http://git.trystack.cn/kanaka/noVNC.git
SPICE_REPO=http://git.trystack.cn/git/spice/spice-html5.git

# Define images to be automatically downloaded during the DevStack built process.
DOWNLOAD_DEFAULT_IMAGES=False
IMAGE_URLS="http://images.trystack.cn/cirros/cirros-0.3.4-x86_64-disk.img"

# Credentials
DATABASE_PASSWORD=passwd
ADMIN_PASSWORD=passwd
SERVICE_PASSWORD=passwd
SERVICE_TOKEN=passwd
RABBIT_PASSWORD=passwd

$ ./stack.sh  //开始安装
```

*注意*

```shell
//若出现错误，排错后执行
$ ./unstack.sh
$ ./stack.sh
```

完成安装

<br>
<img src="/assets/post_pictures/devstack_ubuntu_server.png" width="650">
&nbsp;
<br>

- 测试

浏览器访问`http://192.168.0.221/dashboard`

### 参考

- [官方文档](https://docs.openstack.org/developer/devstack/)
- [DevStack环境搭建](http://blog.csdn.net/mygrus/article/details/53816022)
- [OpenStack项目系列介绍（3） Devstack](http://www.chenshake.com/openstack-project-series-3-devstack/)