---
title: Nginx端口复用
date: 2020-05-06 20:32:21
categories: 前端
---

## 0x00 遇到的问题

想把自己的多个单页应用（SPA）部署在线上nginx服务器上运行，但是80端口只有一个，不可能把所有的应用都放在nginx根目录下运行。于是想到了通过路径对应用加以划分，从而实现端口复用。

## 0x01 解决方案

利用nginx可以配置多个location的功能，为每一个单页应用配置自己的location，从而实现通过不同的路径访问不同应用的目的。nginx配置如下：

```
server {
    listen       'your port';
    server_name  'your ip';

    location  /app1 {
        alias /var/www/path1;
        index index.html index.htm;
    }

    location  /app2 {
        alias /var/www/path2;
        index index.html index.htm;
    }

    location  /image {
        alias /var/www/image;
        index index.html index.htm;
    }
}

```

**这里需要注意的是nginx里location配置中root和alias的区别**
 
> root和alias是系统文件路径的设置。 
> root用来设置根目录，而alias用来重置当前文件的目录。

可以参考下面例子：
```
location /img/ {
    alias /var/www/image/;
}
#若按照上述配置的话，则访问/img/目录里面的文件时，ningx会自动去/var/www/image/目录找文件

location /img/ {
    root /var/www/image;
}
#若按照这种配置的话，则访问/img/目录下的文件时，nginx会去/var/www/image/img/目录下找文件。

```
[nginx 中配置多个location并解决js/css/jpg/等的加载问题](https://blog.csdn.net/ZHangFFYY/article/details/78494637)
