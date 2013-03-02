---
layout: post
title: 运用比较纯的CSS打造很Web2.0的按钮
---

警告：如果你在使用IE浏览此文，那么请回避一下吧! 什么，你用的还是IE6？你真奥特曼(推荐你去打小怪兽)！

先上图，所谓有图有真相。

![CSS按钮效果图](/images/css-buttons/result.png)

如果您觉得图片上这些按钮不够2.0，那没办法，请回避吧！

从图片中，我们可以看到，我们要解决如下几个问题：

* 背景色渐变
* 圆角框
* 阴影效果

就目前而言，要实现如上的效果，我们可以通过使用PNG图片来实现，但熟悉的人都知道，要做出这样的效果来，还是需要花费很多的经力的。更何况还要画出这么多种配色（感谢一下辛苦的前台工程师们）。

接下来，就让我们看下使用CSS(主要是CSS3)怎么来做吧。


### 背景色渐变

我们首先来解决第一个问题，背景色渐变。CSS3是支持背景色渐变的。对于在Firefox 3.6里面使用背景色渐变，可以参考以下一下这个网址:http://css-tricks.com/forums/viewtopic.php?f=8&t=3998，而WebKit引擎的可以参考这个[Introducing CSS Gradients](http://webkit.org/blog/175/introducing-css-gradients/)。但在此处，我还将用比较传统的方式来实现。经常，我们会画一些渐变的图片来做背景，如图：

![gradient](/images/css-buttons/gradient-pics_thumb.gif)

然后我们可以使用css设置`background-image`来实现渐变，而对于悬停(hover)，则可通过设置`background-position`来实现。但这样我们会发现，但需要大量的配色时，要画出这一张张的图片来，还是一件比较耗体力的事（就算你的Photoshop里面存了一大堆这样的样式）。

仔细一想,其实这种渐变颜色基调往往都是一致的,而且往往是从较亮的颜色变到次亮点的颜色(我相信不会有人会要一个蓝变到红的按钮)。那么，我们想，既然是从亮变到暗，而且颜色基调一致，这不就和我们生活中把光从顶部往下照到一块纯色的布上的效果是一样的嘛。那么，如何模拟这种效果呢？很简单，画一张从白色到透明渐变的图往一个纯色的区域上一罩不就OK啦。PhotoShop中的渐变图：

![PS gradient](/images/css-buttons/ps-white-gradient_thumb.png)

CSS如下:

{% highlight css %}
.orange{
	background: #FF5C00 url(images/light-overlay.png) repeat-x scroll 0 0;
}
.orange:hover {
	background-color: #D45500;
}
{% endhighlight %}

这样呢，我们就以一种相对比较简单的方式解决了能够满足大多数场合需求的背景色渐变效果。效果图如下：

![背景色渐变效果图](/images/css-buttons/effect-1_thumb.png)


### 圆角框

接下来，我们看看圆角框。按照我们现在的习惯，圆角框一般都是通过图片来实现。当然也有通过纯CSS(2.0)实现的，园子里有人介绍过。

但那样会多写一丁点HTML。幸运的是CSS3支持圆角框，而且目前那些真正叫做的浏览器的浏览器也是支持圆角框的。只是写法稍微有点不同而已。如下:

{% highlight css %}
.orange{
	-moz-border-radius: 5px;
	-webkit-border-radius: 5px;
}
{% endhighlight %}

明眼人一看就知道，上面那个是针对Firefox用的，下面是针对webkit内核的浏览器用的。效果图:

![圆角框效果图](/images/css-buttons/effect-gradient_thumb.png)


### 阴影效果

在平常切页面(psd->html)的过程中，我最不喜欢做的就是两件事：一件是切圆角框，另外一件是切阴影，第三件便是切带阴影的圆角框(我讨厌数数数不清的人)。

但是，自从看到下面的代码之后，哥笑了。


先看效果图：

![加阴影后效果图](/images/css-buttons/effect-2_thumb.png)

这里面，需要特别指出的是，在CSS3里面，我们可以用RGBA模式选取颜色。所谓RGBA就是在原来的RGB中加了个A（废话）。这个A就是指Alpha，即透明度。合起来，我们就叫RGBA。然后写css时就可以采用rgba(0,0,0,0.5)这样的写法了。

对于-moz-box-shadow的写法可以参考[-moz-box-shadow](https://developer.mozilla.org/En/CSS/-moz-box-shadow)

嗯，阴影有了，写法如此简单。什么？文字不够2.0？好吧，继续加码：
{% highlight css %}
orange {
	text-shadow: 0 -1px 1px rgba(0,0,0,0.8);
}
{% endhighlight %}

还是明眼人就知道，text-shadow就是给文字加阴影。

哦耶，最终效果图：

![最终效果图](/images/css-buttons/effect-0_thumb.png)

text-shadow的写法可以参考[text-shadow](https://developer.mozilla.org/en/CSS/text-shadow)。

最终的CSS代码如下:
{% highlight css %}
.orange,.magenta,.blue,.green,.red {
	display: inline-block;
	width: 150px;
	height: 36px;
	line-height: 36px;
	color: #ffffff;
	text-decoration: none;
	text-align: center;
	font-family: "Courier New", Courier, monospace;

	font-weight: bold;	  
	background: transparent url(images/light-overlay.png) repeat-x scroll 0 0;
	margin: 5px;
	border-bottom: 1px solid rgba(0,0,0,0.25);
	-moz-border-radius: 5px;
	-webkit-border-radius: 5px;
	-moz-box-shadow: 0 1px 3px rgba(0,0,0,0.5);

	-webkit-box-shadow: 0 1px 3px rgba(0,0,0,0.5);
	text-shadow: 0 -1px 1px rgba(0,0,0,0.8);
}

.orange {
	background-color: #FF5C00;
}
.orange:hover {
	background-color: #D45500;
}
.blue {
	background-color: #2DAEBF;
}
.blue:hover {
	background-color: #007D9A;
}
.green {
	background-color: #91BD09;
}
.green:hover {
	background-color: #749A02;
}
.red{
	background-color:#E33100;
}
.red:hover {
	background-color:#872300;
}
.magenta{
	background-color:#A9014B;
}
.magenta:hover {
	background-color:#630030;
}
{% endhighlight %}

Html是酱子的:
{% highlight html%}
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
	 <meta content="text/html; charset=utf-8" http-equiv="Content-Type" />
	 <title>使用比较纯的CSS创建很Web2.0的按钮</title>
	 <style type="text/css">
 .white-area,.dark-area {
	 padding: 10px;
	 margin: 0px;
	 -moz-border-radius: 5px;
	 -webkit-border-radius: 5px;
 }
 .white-area {
	 background-color: #ffffff;
 }
 .dark-area {
	 background-color: #333333;
 }
 .columns {
	 float: left;
	 margin-left: 10px;
 }
 .clear {
	 clear: both;
 }
</style>
	 <link rel="stylesheet" href="css3buttons.css" charset="utf-8" />
</head>

<body>
	 <div class="white-area">
		  <a href="javascript:void(0);" class="orange">Web 2.0</a>
		  <a href="javascript:void(0);" class="magenta">Web 2.0</a>
		  <a href="javascript:void(0);" class="red">Web 2.0</a>
		  <a href="javascript:void(0);" class="blue">Web 2.0</a>
		  <a href="javascript:void(0);" class="green">Web 2.0</a>
	 </div>
	 <div class="dark-area">
		  <a href="javascript:void(0);" class="orange">Web 2.0</a>
		  <a href="javascript:void(0);" class="magenta">Web 2.0</a>
		  <a href="javascript:void(0);" class="red">Web 2.0</a>
		  <a href="javascript:void(0);" class="blue">Web 2.0</a>
		  <a href="javascript:void(0);" class="green">Web 2.0</a>
	 </div>
</body>
</html>
{% endhighlight %}

要看更丰富的效果，可以下源码，点击[下载](/downloads/ButtonsByCSS3.zip)呀！


### 结语

嗯，感谢CCTV、感谢KTV，感谢。。。唉呀，谁扔的臭鸡蛋！！！

说正经的，本人不是专业美工，更谈不上UI设计师。我只是一位码农，不管是写代码的的农民也好，还是天天除了写代码就玩农场的程序员也好，我就是这样的。之所以写这篇东西，也是因为看到了这个地方[Awesome buttons with css3](http://www.zurb.com/article/266/super-awesome-buttons-with-css3-and-rgba)，感觉很好玩，就连翻译带自己的理解加上周未休息的时间（农场里面菜还没熟，等得寂寞了），写下这个东西了。

谢谢观赏!