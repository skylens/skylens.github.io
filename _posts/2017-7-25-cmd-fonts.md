---
layout: post_layout
title: 更改Windows10的终端字体
time: 2017年7月25日 星期二
location: 昆明
pulished: true
excerpt_separator: "```"
---

先下载`YaHei Consolas Hybrid`字体

win + R -> regedit

```
计算机\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Console\TrueTypeFont
```

新建一个字符串值 

修改值为`YaHei Consolas Hybrid`

效果图

<br>
<img src="/assets/post_pictures/cmd_font.png" width="650">
&nbsp;
<br>