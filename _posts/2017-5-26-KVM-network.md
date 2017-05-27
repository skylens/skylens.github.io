---
layout: post_layout
title: KVM网络设置
time: 2017年5月26日 星期五
location: 昆明
pulished: true
excerpt_separator: "```"
---

### KVM虚拟机网络连接有两种方式

- 用户网络（User Networking）：让虚拟机访问主机、互联网或本地网络上的资源的简单方法，但是不能从网络或其他的客户机访问客户机，性能上也需要大的调整。NAT方式。

- 虚拟网桥（Virtual Bridge）：这种方式要比用户网络复杂一些，但是设置好后客户机与互联网，客户机与主机之间的通信都很容易。Bridge方式。

### KVM默认的default是用户网络
