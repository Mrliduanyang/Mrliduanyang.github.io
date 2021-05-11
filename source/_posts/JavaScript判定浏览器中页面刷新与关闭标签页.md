---
title: JavaScript判定浏览器中页面刷新与关闭标签页
date: 2020-05-06 21:23:52
categories: 前端
---

## 0x00 原理概述

借助LocalStorage和onunload事件。
LocalStorage中存储数据可以长期保留，除非被手动清除。
浏览器在关闭页面和刷新页面时，都会触发onunload事件。所以可以在onunload事件中将时间戳存储在LocalStorage中，间接实现对页面刷新和关闭的判断。

## 0x01 实现

```
<script>
    let lastTime = localStorage.getItem('lastTime')
    let currentTime = new Date().getTime()
    if ((currentTime - lastTime) < 5000) {
        console.log('Refresh')
    } else {
        console.log('Close')
    }
    window.onunload = function () {
        localStorage.setItem('lastTime', new Date().getTime())
    } 
</script>
```

代码分析：

当页面第一次载入时，`locaStorage`中没有`lastTime`，所以`if`条件不成立，会先执行一次关闭时代码。

当页面刷新时，首先触发`onunload`事件，在`onunload`中，将时间戳存放在`localStorage`中。之后页面开始刷新，刷新之后立即从`localStorage`中获取页面刷新前存放的时间戳`lastTime`，并与页面刷新后的时间戳`currentTime`做比较，若差值小于5000，则认为页面是刷新。

当关闭页面时，首先触发`onunload`事件，在`onunload`中，将时间戳存放在`localStorage`中，之后页面被关闭。当页面再次被打开时立即从`localStorage`中获取页面刷新前存放的时间戳`lastTime`，并与当前页面的时间戳`currentTime`做比较，若差值大于5000，则认为之前页面被关闭，执行一次关闭时代码。

代码也是有逻辑漏洞的。第一次加载时，便会执行关闭时代码；页面关闭后，其实是没有执行关闭时代码的，而是当下次再打开页面，判断和上次关闭的时间间隔大于阈值时才执行关闭时代码。也就是说，当打开、关闭页面的时间间隔小于阈值时，还是会被认为是刷新。当时间间隔大于阈值，才被认为是关闭。

代码仅供参考。反正是实现了我的需求——在页面刷新时不清除`LocalStorage`中数据，在页面`关闭`时清除`LocalStorage`中数据。
