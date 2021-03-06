---
layout: post_layout
title: 修改GRUB2的主题
time: 2017年5月14日 星期天
location: 昆明
pulished: true
excerpt_separator: "```"
---

### 什么是GRUB2

GRUB2是一个来自GNU项目的启动引导程序。GRUB是多启动规范的实现，它允许用户可以在计算机内同时拥有多个操作系统，并在计算机启动时选择希望运行的操作系统。

### GRUB2主题

GRUB2的默认主题有一点点难看，但GRUB2支持修改主题，你可以自己写一款自己喜欢的主题，当然也可以找别人写好的主题。

怎样修改主题

```bash
# git clone https://github.com/Se7endAY/grub2-theme-vimix.git  //把Vimix主题克隆到本地
# cd grub2-theme-Vimix/
# mv Vimix/ /boot/grub/themes/  //把Vimix主题整个目录移动到GRUB的主题目录下
# vim /etc/default/grub  //修改GRUB配置文件，找到GRUB_THEME，做如下修改
GRUB_THEME="/boot/grub/themes/Vimix/theme.txt"

# grub-mkconfig -o /boot/grub/grub.cfg  //更新一下GRUB，这条命令的作用和update-grub一样
```

### 第三方主题整理

[**Vimix**](https://github.com/Se7endAY/grub2-theme-vimix)

[**Ettery**](https://github.com/Dacha204/grub2-themes-Ettery)

[**breeze**](https://github.com/ysard/grub-breeze-kali-theme)

[**Elegant**](https://github.com/maximilienGilet/Elegant-GRUB2)

[**archxion**](https://github.com/Generator/Grub2-themes)

[**arch-silence**](https://github.com/fghibellini/arch-silence)

### 用了第三方主题还是不够精致

启动菜单中还有一个`Advanced options for XX linux`，一般我们用不到，怎么隐藏它呢！

```shell
# vim /boot/grub/grub.cfg
找到Advanced options部分把它们全部注销
```

装了Windows的同学，windows的选项是`Windows Boot Manager (on /dev/sda2)`这样的，我们可以找到这部分，把这部分替换过成`Windows 10`

### 最后的结果

<br>
<img src="/assets/post_pictures/EtteryPreview.png" width="650">
&nbsp;
<br>
这样就简洁精致许多了
