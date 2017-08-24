---
layout: post_layout
title: Nginx 设置索引目录列表
time: 2017年8月24日 星期四
location: 昆明
pulished: true
excerpt_separator: "```"
---

### 前言

Apache 有漂亮的索引目录列表，当然 Nginx 也有，但是 Nginx 默认是不开启的，怎么为 Nginx 打开索引目录列表呢？

### 操作

+ 安装 Nginx

```shell
$ sudo apt install nginx
```

+ 配置 Nginx

```shell
$ sudo vim /etc/nginx/sites-available/default
...
location / {
    autoindex on;   //开启目录浏览功能
    autoindex_exact_size off;   //关闭详细文件大小统计，让文件大小显示MB、GB单位，默认为b
    autoindex_localtime on;   //开启以服务器本地时区显示文件修改日期
    ...                
}
...

$ sudo /etc/init.d/nginx reload
```

+ 做上面的配置你已经打开了 Nginx 的索引目录列表功能，但是你想索引目录列表漂亮一点

    - 安装 Nginx 拓展(里面有一款 Nginx 的索引目录列表的主题)

    ```shell
    $ sudo apt install nginx-extras
    ```

    - 修改之前的配置(当然要把刚刚的配置删除了，做新的修改才会加载新的主题)

    ```shell
    $ sudo vim /etc/nginx/sites-available/default
    ...
    location / {
        fancyindex on;
        fancyindex_exact_size off;
        fancyindex_localtime on;
        ...                
    }
    
    ```