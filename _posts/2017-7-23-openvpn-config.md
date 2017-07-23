---
layout: post_layout
title: 编译安装openvpn
time: 2017年7月23日 星期天
location: 昆明
pulished: true
excerpt_separator: "```"
---

### 前言

在centos7(需要两块网卡)上编译安装openvpn，采用VMware虚拟机做为实验环境，CentOS 7 作为 VPN Server，windows10作为客户端

### 开始

+ 规划
<br>
<img src="/assets/post_pictures/open-config.png" width="650">
&nbsp;
<br>
+ 安装

  ```shell
  # yum install wget gcc make openssl* vim -y 
  # mkdir openvpn
  # cd openvpn
  # wget http://www.oberhumer.com/opensource/lzo/download/lzo-2.06.tar.gz
  # tar -xvf lzo-2.06.tar.gz
  # cd lzo-2.06
  # ./configure
  # make
  # make install
  # cd ..
  # wget http://swupdate.openvpn.org/community/releases/openvpn-2.2.2.tar.gz
  # cd openvpn-2.2.2
  # ./configure --with-lzo-headers=/usr/local/include --with-lzo-lib=/usr/local/lib
  # make
  # make install
  # which openvpn
  ```

+ 生成证书

  ```shell
  # cd easy-rsa/2.0
  # cp var var.bak
  # vim var
  //修改如下内容
  export KEY_COUNTRY="CN"
  export KEY_PROVINCE="KM"
  export KEY_CITY="Kunming"
  export KEY_ORG="skylens"
  export KEY_EMAIL="skylens116@qq.com"
  export KEY_EMAIL=skylens116@qq.com
  export KEY_CN=CN
  export KEY_NAME=skylens
  export KEY_OU=skylens
  export PKCS11_MODULE_PATH=changeme
  export PKCS11_PIN=1234

  # source vars
  # ./clean-all   //清除之前生成的所有证书
  # ./build-ca    //生成CA证书
  # ./build-key-server server   //生成名为server的服务端证书和私钥
  # ./build-key test			 //生成名为test的客户端证书和私钥
  # ./build-key-pass ett		 //生成名为ett的客户端加密证书和私钥
  # ./build-dh                 //创建迪菲·赫尔曼密钥
  # openvpn --genkey --secret keys/ta.key   //生成tls加密密钥
  # cp -ap keys/ /etc/openvpn/   //把生成的密钥拷贝到opevpn的配置目录下 (其实是ca.crt server.crt server.key dh1024.pem ta.key 这些文件，并不需要靠所有)
  # cd /etc/openvpn/keys
  # ll
  ```

+ 修改服务端配置文件(**注意:/etc/openvpn/下只能有一个.conf的配置文件,不然使用官方脚本启动会失败**)

  ```shell
  # cp /root/openvpn/openvpn-2.2.2/sample-scripts/server.conf /etc/openvpn/  //这个文件注释太多了，可以参考下面的配置
  # vim /etc/openvpn/server.conf

  local 192.168.42.153
  port 52115
  proto tcp
  dev tun
  ca /etc/openvpn/keys/ca.crt
  cert /etc/openvpn/keys/server.crt
  key /etc/openvpn/keys/server.key
  dh /etc/openvpn/dh1024.pem
  # tls 加密 服务端 0 
  tls-auth /etc/openvpn/keys/ta.key 0
  server 10.8.0.0 255.255.255.0
  push "route 172.0.0.0 255.255.255.0"
  ifconfig-pool-persist /etc/openvpn/ipp.txt
  keepalive 10 120
  comp-lzo
  persist-key
  persist-tun
  status /etc/openvpn/openvpn-status.log
  verb 3
  client-to-client
  duplicate-cn
  log /var/log/openvpn.log
  ```

+ 防火墙及内核转发设置

  ```shell
  # /etc/init.d/iptables stop    //刚开始可以关闭，等测试通过了再来配置防火墙
  # iptables -A INPUT -p tcp --dport 52115 -j ACCEPT
  # vim /etc/sysctl.conf
  //添加
  net.ipv4.ip_forward = 1

  # sysctl -p   //检查内核转发设置
  ```

+ 时间同步设置

  ```shell
  # echo "# time sync" >>/var/spool/cron/root
  # echo '*/5 * * * * /usr/sbin/ntpdate cn.ntp.org.cn >/2>&1' >>/var/spool/cron/root
  # crontab -l
  ```

+ 客户端设置

  ```shell
  # cp /root/openvpn/openvpn-2.2.2/sample-scripts/client.conf client.ovpn  //这个文件注释太多了，可以参考下面的配置
  # vim client.ovpn

  client
  proto tcp
  dev tun
  remote 192.168.42.153 52115
  resolv-retry infinite
  nobind
  persist-key
  persist-tun
  ca ca.crt
  cert test.crt
  key test.key
  # tls 加密 客户端 1
  tls-auth ta.key 1
  ns-cert-type server
  comp-lzo
  verb 3

  // 把client.ovpn及客户端对应的证书及密钥拷贝到客户端上并都在同一级目录(ca.crt test.crt test.key ta.key)
  ```

+ 测试

  在windows10上 ping 172.0.0.128，不能 ping 通，打开 OpenVPN 客户端，之后能 ping 通


