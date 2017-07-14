---
title: H5全屏视频交互的一点研究
date: 2017-07-14 16:08:20
tags:
  - H5
  - video
  - inline
categories:
  - H5
---

项目要求一个在微信可全屏可交互的视频，稍微做了一些研究，稍作一点总结

## &lt;video/&gt;标签

`video`标签最简单的用法

	<video src="movie.ogg" controls="controls">   
	您的浏览器不支持 video 标签。   
	</video>;
	
或者，根据浏览器支持的视频类型播放不同的格式:

	<video src="movie.ogg" controls="controls">
		<source src="movie.ogg" type="video/ogg" />
  		<source src="movie.mp4" type="video/mp4" />   
		您的浏览器不支持 video 标签。   
	</video>;


`video`标签有几个常用的属性:  


属性       | 值         | 描述     |
----------|------------|---------|
autoplay	| autoplay   | 如果出现该属性，则视频在就绪后马上播放。
controls	| controls   | 如果出现该属性，则向用户显示控件，比如播放按钮。
height    | pixels	   | 设置视频播放器的高度。
loop      | loop       | 如果出现该属性，则当媒介文件完成播放后再次开始播放。
preload   | preload	   | 如果出现该属性，则视频在页面加载时进行加载，并预备播放。如果使用 "autoplay"，则忽略该属性。规定是否预加载视频。<br>可能的值：<br>auto - 当页面加载后载入整个视频<br> meta - 当页面加载后只载入元数据<br>none - 当页面加载后不载入视频
src       | url        | 要播放的视频的 URL。
width     | pixels     | 设置视频播放器的宽度。
poster    | url        | 定视频下载时显示的图像，或者在用户点击播放按钮前显示的图像。如果未设置该属性，则使用视频的第一帧来代替。规定图像文件的 URL。<br>可能的值：<br>绝对 URL - 指向另一个网站（比如 href="http://www.example.com/poster.jpg"）<br>相对 URL - 指向网站内的文件（href="poster.jpg"）
width     | pixels     | 设置视频播放器的宽度。
autoplay  | autoplay   | 规定一旦视频就绪马上开始播放。如果设置了该属性，视频将自动播放。<br>但是这个属性在移动端大多不能使用，如iOS的要求至少有一次用户触摸事件之后才能开始播放视频。

以上是浏览器通用属性，由于这次使用微信作为传播载体，所以有必要再介绍几个QQ浏览器内核支持的特定属性：

属性          | 值         | 描述     |
-------------|------------|---------|
playsinline  | 

data-index="1" x-webkit-airplay="" playsinline="" webkit-playsinline=""
                   x5-video-player-fullscreen="false" x5-video-player-type="h5"

http://blog.csdn.net/qq_16494241/article/details/62046891

## 横屏播放

