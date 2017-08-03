---
title: 使用viewport进行移动端H5页面适配
date: 2017-07-20 16:11:49
tags:
  - H5
  - viewport
categores:
  - H5
---

移动端机型适配是个比较头疼的问题，适配的方式也很多，今天主要讨论下使用viewport进行适配


## ViewPort

 网上能找到的东西就不重复了请看这两篇博客[《移动H5页面开发多屏适配 - viewport和像素篇》](http://www.jianshu.com/p/dd25d84647e2)以及[《移动前端开发之viewport的深入理解》](http://www.cnblogs.com/2050/p/3877280.html)
 
 总结起来，当页面不设置ViewPort时，移动设备上的浏览器都会把自己默认的viewport设为980px或1024px（也可能是其它值，这个是由设备自己决定的），总之是一个比较大的值为了能展示pc上的网页。
 
 对于一个专门为移动端设计的页面，980的宽度显然太大，所以需要重新设置viewport
 	
 	<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0">
 	
 在苹果的规范中，meta viewport 有6个属性(暂且把content中的那些东西称为一个个属性和值)，如下：
 

属性       | 描述     |
----------|---------|
width	| 设置layout viewport  的宽度，为一个正整数，或字符串"width-device"
initial-scale	| 设置页面的初始缩放值，为一个数字，可以带小数
minimum-scale	| 允许用户的最小缩放值，为一个数字，可以带小数
maximum-scale	| 允许用户的最大缩放值，为一个数字，可以带小数
height	| 设置layout viewport  的高度，这个属性对我们并不重要，很少使用
user-scalable| 	是否允许用户进行缩放，值为"no"或"yes", no 代表不允许，yes代表允许

针对这几个属性，我做了一个简单的测试，测试数据可见这篇[文章]()
<!--more-->

在iphone和ipad上，无论你给viewport设的宽的是多少，如果没有指定默认的缩放值，则iphone和ipad会自动计算这个缩放值，以达到当前页面不会出现横向滚动条(或者说viewport的宽度就是屏幕的宽度)的目的。
