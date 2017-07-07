---
title: 使用canvas绘制图片
date: 2017-07-07 15:45:45
tags:
  - h5
  - canvas
  - drawImage
categories: h5

---

## 使用Canvas绘制图片
绘制图片CanvasRenderingContext2D对象的哪些主要属性和方法：

`drawImage(mixed image, int x, int y)`  
以canvas上指定的坐标点开始，按照图像的原始尺寸大小绘制整个图像。这里的image可以是Image对象，也可以是Canvas对象(下同)。

`drawImage(mixed image, int x, int y, int width, int height)`  
以canvas上指定的坐标点开始，以指定的大小(width和height)绘制整个图像，图像将根据指定的尺寸自动进行相应的缩放。

`drawImage(mixed image, int imageX, int imageY, int imageWidth, int imageHeight, int canvasX, int canvasY, int canvasWidth, int canvasHeight)`  
将指定图像的局部图像(以(imageX, imageY)为左上角、宽度为imageWidth、高度为imageHeight的矩形部分)绘制到canvas中以(canvasX,canvasY)为左上角坐标、宽度为canvasWidth、高度为canvasHeight的矩形区域中  
  
很坑，drawImage()方法具有三种不同的变体，每个方法变体允许接收的参数不仅数量不同，连参数的含义也不尽相同。
{% codeblock lang:javascript %}
  
	var canvas = document.getElementById("myCanvas");
	//简单地检测当前浏览器是否支持Canvas对象，以免在一些不支持html5的浏览器中提示语法错误  
	if(canvas.getContext){  
		//获取对应的CanvasRenderingContext2D对象(画笔)  
		var ctx = canvas.getContext("2d");  
		//创建新的图片对象  
		var img = new Image();  
		//指定图片的URL  
		img.src = "http://www.365mini.com/image/google_logo.png";  
		//浏览器加载图片完毕后再绘制图片  
		img.onload = function(){  
			//以Canvas画布上的坐标(10,10)为起始点，绘制图像  
			ctx.drawImage(img, 10, 10);  
		};  
	}
{% endcodeblock %}

## 清除图片
CanvasRenderingContext2D对象提供了一个可以永远重复使用的橡皮擦——clearRect()方法。

`clearRect(x, y, width, height)`  

CanvasRenderingContext2D对象的`clearRect()`方法用于清除canvas内以指定坐标点(x,y)为左上角、宽度为width、高度为height的矩形区域中的所有图形像素。

## 正确设置Canvas高度
<font color="red">**在js中设置canvas的宽高时，如果设置方式不正确，或者在cass中设置时，在绘制图像时就会出现拉伸的情况。这是因为canvas的默认宽高为300px*150px，在css中设置canvas的宽高，实际上是把canvas在300px*150px的基础上进行了拉伸。所以绘制出来的图像会发生变形。**</font>  
{% codeblock lang:javascript %}    
	  
	var winWidth = $(window).width();
	var winHeight = $(window).height();
	$("#cv").get(0).width = winWidth;
	$("#cv").get(0).height = winHeight;
{% endcodeblock %}
