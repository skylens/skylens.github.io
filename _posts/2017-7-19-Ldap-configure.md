---
layout: post_layout
title: 编译安装ldap
time: 2017年7月19日 星期三
location: 昆明
pulished: true
excerpt_separator: "```"
---

### 前言

在ubuntu server 14.04下编译安装ldap服务

### 开始

+ 准备

```shell
$ sudo apt-get install gcc make
```

+ 先编译安装BerkeleyDB(注意要选择5.0-5.1或4.4-4.8的版本)

```shell
$ wget http://download.oracle.com/berkeley-db/db-5.1.29.tar.gz
$ tar -xvf db-5.1.29.tar.gz
$ cd db-5.1.29
$ cd build_unix
$ ../dist/configure --prefix=/usr/local/BerkeleyDB
$ make
$ sudo make install
$ sudo cp /usr/local/BerkeleyDB/include/* /usr/include/
$ sudo cp /usr/local/BerkeleyDB/lib/* /usr/lib/
```

+ 编译安装openldap

```shell
$ wget ftp://ftp.openldap.org/pub/OpenLDAP/openldap-release/openldap-2.4.45.tgz
$ tar -xvf openldap-2.4.45.tgz
$ cd openldap-2.4.45
$ ./configure --prefix=/usr/local/openldap
$ make depend
$ make
$ make install
```
+ 设置ldap

```shell
$ sudo vim /usr/local/openldap/etc/openldap/slapd.conf
...
suffix          "dc=example,dc=com"
rootdn          "cn=admin,dc=example,dc=com"
rootpw          secret
include /usr/local/openldap/etc/openldap/schema/core.schema
include /usr/local/openldap/etc/openldap/schema/cosine.schema
include /usr/local/openldap/etc/openldap/schema/inetorgperson.schema
include /usr/local/openldap/etc/openldap/schema/corba.schema
include /usr/local/openldap/etc/openldap/schema/dyngroup.schema
include /usr/local/openldap/etc/openldap/schema/java.schema
include /usr/local/openldap/etc/openldap/schema/misc.schema
include /usr/local/openldap/etc/openldap/schema/nis.schema
include /usr/local/openldap/etc/openldap/schema/openldap.schema

$ /usr/local/openldap/libexec/slapd   //启动sldapd服务
$ vim admin.ldif
dn: dc=example,dc=com
objectClass: dcObject
objectClass: organization
dc: example
o: Example Company

dn: cn=admin,dc=example,dc=com
objectClass: organizationalRole
objectClass: top
cn: admin
$ sudo vim /etc/environment   //修改环境变量把openldap的安装目录添加进去
$ ldapadd -x -D "cn=admin,dc=example,dc=com" -w secret -f admin.ldif   //添加数据库 
$ ldapsearch -x -b 'dc=example,dc=com'   //检查数据库是否添加成功
```
