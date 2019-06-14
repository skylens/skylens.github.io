---
layout: post_layout
title: 安装补丁“此更新不适用于你的计算机”解决办法
time: 2019年6月14日 星期五
location: 文山
pulished: true
excerpt_separator: "```"
---

### 前言

`windows server 2012 R2` 安装补丁出现“此更新不适用于你的计算机”

### 处理办法

在 D 盘创建 patch 目录 --> 解压缩 msu 文件到 patch 目录 --> 使用 dism 安装 cab 格式的补丁

```cmd
mdkir D:\patch
expand –F:* D:\patch\Windows8.1-KB4503290-x64.msu D:\patch
dism /online /Add-Package /PackagePath:D:\patch\Windows8.1-KB4503290-x64.cab
```