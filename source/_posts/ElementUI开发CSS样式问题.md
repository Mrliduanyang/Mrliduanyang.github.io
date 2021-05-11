---
title: ElementUI开发CSS样式问题
date: 2020-05-06 20:34:32
categories: 前端
---

## 0x00 遇见的问题

最近基于Element UI开发项目，遇到了一个不大不小的问题。在Dev环境中，自己写的CSS样式是可以覆盖掉Element中的样式，但在Prod环境中，却出现了自己的样式被Element覆盖掉的情况。

## 0x01 原因

从网上找了一下原因，主要是因为Dev环境和Prod环境，CSS样式的设置方式不一样导致。具体来说，Dev环境里是通过js append style标签来加入你的Element的样式，而Prod环境Element基础样式是通过link的方式引入的。

## 0x02 解决方案

在 src 目录下，找到main.js文件并打开，将import 'element-ui/lib/theme-chalk/index.css';和import App from './App';顺序调换一下即可，将Element Ui的CSS的样式放到最前面。
