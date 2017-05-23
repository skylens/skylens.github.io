---
layout: post_layout
title: Hadoop分布式部署
time: 2017年5月23日 星期二
location: 昆明
pulished: true
excerpt_separator: "```"
---

### 说明

本次实验使用了两台安装Debian的电脑，IP地址分别为192.168.1.121，192.168.1.122

### 部署

**1.用户设置**

```shell
$ sudo useradd -m hadoop -s /bin/bash  //添加hadoop用户，两个节点都要执行
$ sudo passwd hadoop  //设置hadoop用户的密码，两个节点都要执行
$ sudo adduser hadoop sudo  //为hadoop用户增加管理员权限，两个节点都要执行
$ sudo su - hadoop  //切换到hadoop用户，两个节点都要执行
```

**2.源设置**

```shell
$ ping 163.com  //检查网络,两个节点都要执行
$ sudo vim /etc/apt/sources.list  //修改源，两个节点都要执行
deb http://mirrors.ustc.edu.cn/debian/ jessie main contrib non-free
deb http://mirrors.163.com/debian/ jessie main non-free contrib

$ sudo apt-get update  //更新源，两个节点都要执行
```

**3.主机名设置**

```shell
$ echo "Master" > /etc/hostname  //设置主节点的主机名
$ echo "Slave1" > /etc/hostname  //设置分节点的主机名
$ sudo vim /etc/hosts  //修改hosts，两个节点都要执行
192.168.1.121   Master
192.168.1.122   Slave1

$ ping -c 3 Master  //检查设置，两个节点都要执行
$ ping -c 3 Slave1  //检查设置，两个节点都要执行
```

**4.ssh设置**

```shell
$ ssh-keygen -t rsa  //生成密钥对,主节点上完成
$ ssh-copy-id hadoop@Master  //把公钥上传到主节点上
$ ssh-copy-id hadoop@Slave1  //把公钥上传到分节点上
$ ssh Master  //测试登录主节点，两个节点都要执行
$ ssh Slave1  //测试登录分节点，两个节点都要执行
```

**5.安装Java环境及hadoop**

```shell
$ sudo apt-get install  openjdk-8-jre openjdk-8-jdk  //两个节点都要安装
$ wegt http://mirror.bit.edu.cn/apache/hadoop/common/hadoop-2.7.1/hadoop-2.7.1.tar.gz
$ sudo tar -zxvf hadoop-2.7.1.tar.gz -C /usr/local  //两个节点都要执行
$ cd /usr/local
$ sudo mv hadoop-2.7.1 hadoop
$ sudo chown -R hadoop ./hadoop
```

**6.环境变量设置**

```shell
$ vim ~/.bashrc  //添加，两个节点都要执行
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64/
export PATH=$PATH:/usr/local/hadoop/bin:/usr/local/hadoop/sbin

$ $JAVA_HOME/bin/java -version  //测试Java环境变量，两个节点都要执行
$ hadoop version  //测试hadoop环境变量，两个节点都要执行
```

**7.初始化**

_注意_

```shell
$ hdfs namenode -format  //主节点上执行，只执行一次
```

**8.启动hadoop，在主节点上执行**

```shell
$ start-dfs.sh
$ start-yarn.sh
$ mr-jobhistory-daemon.sh start historyserver
```

**9.检查节点是否正常**

```shell
$ jps  //查看hadoop启动的进程，两个节点上执行
$ hdfs dfsadmin -report  //在主节点上查看分节点是否正常
```

**10.执行分布式实例**

```shell
$ hdfs dfs -mkdir -p /user/hadoop
$ hdfs dfs -mkdir input
$ hdfs dfs -put /usr/local/hadoop/etc/hadoop/*.xml input
$ hadoop jar /usr/local/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar grep input output 'dfs[a-z.]+'  //运行 MapReduce 作业
```

**11.在主节点上关闭Hadoop集群**

```shell
$ stop-yarn.sh
$ stop-dfs.sh
$ mr-jobhistory-daemon.sh stop historyserver
```