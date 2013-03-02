---
layout: post
title: 让JavaScript中计时器setTimeout/setInterval的回调方法支持参数传递
---

### 1. 背景

在JavaScript中，我们经常要用到Timer，也就是setTimeout或者setInterval这两个方法。例如：

{% highlight js %}
var t1 = setTimeout(function() {
	//TODO: add your logic here
}, 1000);
{% endhighlight %}

同时，我们也经常听到有人报怨说里面的这个回调方法不支持参数传递。有时候，我们想要在里面的function里面用到外部的数据时，只能在外面定义一个变量，如下：

{% highlight js %}
var i = 0;
setTimeout(function() {
	alert(i);
}, 1000);
{% endhighlight %}

那么，我们如果能够里面这个function支持参数传递，是不是感觉要好点呢？比如写成这样[1]：

{% highlight js %}
setTimeout(function(msg) {
	alert(msg); //显示 Hello, world!
}, 1000, 'Hello, world!');
{% endhighlight %}


### 2. 初试

笔者突发奇想地试着将上述代码放到浏览器里面执行了一下：


{% highlight html %}
<!DOCTYPE HTML>
<html>
	<head>
		<title>JavaScript Timer Args Test</title>
	</head>
	<body>
		<script type="text/javascript">
			(function(){
				setTimeout(function(msg) {
					alert(msg); //显示 Hello, world!
				}, 1000, 'Hello, world!');
			})();
		</script>
	</body>
</html>
{% endhighlight %}

令人惊喜的事，这段代码在Firefox 4.0, Chrome 11, Safari 5.0里面尽然能够成功的执行，弹出了Hello, world!  在IE9 中弹出的则是undefined!

![browser navtive support](/images/js-timer-callback/first-try.gif)


### 3. 兼容IE

时至此刻，要想在IE下也能使用，就只有替换掉IE内置的setTimeout/setInterval方法了。如下：

{% highlight js %}
/**
 * @author Hippasus
 */
(function(){ // reset timer for IE
	if (navigator.appName == 'Microsoft Internet Explorer') {
		var _preTimeout = window.setTimeout, _preInterval = window.setInterval;
		 
		window.setTimeout = function(callback, after){
			var l = arguments.length;
			if (l > 2) {
				var args = [];
				for (var i = 2; i < l; i++) {
					args.push(arguments[i]);
				}
				 
				return _preTimeout(function(){
					callback.apply(this, args);
				}, after); //记得返回timer id
			}
			else {
				return _preTimeout.apply(this, arguments);
			}
		}
		 
		window.setInterval = function(callback, cycle){
			var l = arguments.length;
			if (l > 2) {
				var args = [];
				for (var i = 2; i < l; i++) {
					args.push(arguments[i]);
				}
				 
				return _preInterval(function(){
					callback.apply(this, args);
				}, cycle);
			}
			else {
				return _preInterval.apply(this, arguments);
			}
		}
	}
})();
{% endhighlight %}


### 4. 结语

一些比较现代的浏览器都支持js在timer的回调函数中传入参数，只是一般不为人知而已(个人感觉这种写法以后会成为js中的标准)。

希望本文对您有帮助！


[1]: http://nodejs.org/docs/v0.4.5/api/timers.html "NodeJs中的setTimeout语法 setTimeout(callback, delay, [arg], [...])"	
