---
title: Vue修改打包后静态资源路径
date: 2020-05-06 18:58:55
categories: 前端
---

## 0x00 遇到的问题
在nginx上部署Vue项目时，如果将项目直接放在nginx根目录下，客户端访问正常。如果放在子目录下，客户端访问的时候会出现资源加载失败的问题。究其原因，是因为Vue-Cli在对资源进行打包时，都是用的绝对路径，部署在nginx服务器上，对应的就是nginx的根目录。所以导致当项目部署在子目录下的时候无法从子目录加载资源文件。

## 0x01 可行的解决方案
### 1.修改为绝对路径

在Vue项目config -> index.js配置文件中。build时默认的资源打包路径为`assetsPublicPath: '/'`这个'/'对应着nginx的根目录，所以我们将其修改为nginx配置文件中访问项目的路径即可。如我的nginx路径配置如下:

```
location  /weather {
    alias /var/www/Weather/;
    index index.html index.htm;
}
```

那么对应的assetsPublicPath为'/weather/'。

### 2.修改为相对路径
修改相对路径，又有两种方式：
#### （1）
在Vue项目config -> index.js配置文件中。将assetsPublicPath修改为'./'，即相对于index.html的同级路径。
#### （2）
在Vue项目build -> webpack.prod.conf.js配置文件中，找到output，新增publicPath:'./'即可。

#### Tips
通过以上的设置，虽然已经大部分资源引用的路径已经修改为相对路径，但实际上css引用的资源路径还存在问题。因为在webpack中css文件是使用extract-text-webpack-plugin单独进行打包处理。所以需要在build -> utils.js配置文件中，找到ExtractTextPlugin.extract，在参数中新增publicPath:'../../'。

综合以上两种方式，第一种修改绝对路径的方式，不便于服务迁移，因为每次改变项目部署路径，都需要修改源码中的资源打包路径。而第二种相对路径方式，资源访问的起点都是当前路径，不存在对源码的修改，推荐使用。
