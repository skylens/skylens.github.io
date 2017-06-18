---
layout: post_layout
title: Linux命令之cat
time: 2017年6月18日 星期天
location: 昆明
pulished: true
excerpt_separator: "```"
---

### `cat`查看文件内容

```shell
$ cat test.txt
```

### `cat`编辑文件

```shell
$ cat << EOF > test.txt  //覆盖test.txt中的内容，以EOF结束
abc
ABC
EOF
$ cat << EOF >> test.txt  //追加内容到test.txt，以EOF结束
123
ABC
EOF
```
