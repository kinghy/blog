---
title: H5中的长按和滑动手势操作
date: 2017-07-19 13:37:33
tags: 
  - H5
  - touchstart
  - touchend
  - 手势
  - 移动端
  
categories:
  - H5
---

在移动端H5中如果设计了一些需要长按或者滑动的手势操作有可能会和系统默认的一些操作相冲突，所以在编写这些手势时需要做一些特定的处理

## 长按手势

在H5中是没有长按这个时间触发的，所以比较麻烦的是必须自己实现长按的手势判断，下面是一个简单的JS判断代码：

    var press = false
    $("img").on("touchstart",function () {
        press = true;
        setInterval(function () {
            console.log("longpress")
        },500)
    }).on("touchend",function () {
        press = false;
    })

<!--more-->

在chrome上模拟手机端测试下，疗效很好。。。

 <img src="h5-long-push/i1.png" width = "300"  alt="图片名称" align=center />

但是却有个副作用，就是长按之后chrome弹出了默认菜单。

 <img src="h5-long-push/i2.png" width = "300"  alt="图片名称" align=center />
 
虽说我们的目标是移动端，但是测试的时候老是有这个菜单总是碍手碍脚，一定要把他干掉！稍微百度下增加了以下代码：

    //屏蔽长按菜单
    document.body.addEventListener('contextmenu', function(e){
        e.preventDefault();
    });

再一跑，世界清净了！！

### oncontextmenu事件

oncontextmenu 事件在元素中用户右击鼠标时触发并打开上下文菜单。