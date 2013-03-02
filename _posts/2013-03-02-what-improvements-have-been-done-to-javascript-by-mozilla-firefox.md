---
layout: post
title: Mozilla Firefox 这些年对 JavaScript 的改进做了些什么？
---

### 序言

不得不承认，JavaScript算是比较弱的语言，主要表现为如下两个方面：

1. 原生的库与方法太弱，无法满足日常的需求
2. 抽象能力不是很强，影响产能

如果说不是因为Ajax的话，这门语言恐怕早已经被人遗忘，变成了一门没有个好的开始，也没能有善终的语言。

在JavaScript的春天刚开始的时候，它遵循是ECMA-262的第3个版本。Firefox上跑的是JavaScript 1.5, IE6上跑的还叫JScript，Chrome还未出生。

从那个时刻起，经过业界同僚的不断努力，JavaScript才真正慢慢变得强大了起来。而最先得到改善的还是体现在对库与方法的扩展上。[Prototype](http://prototypejs.org/), [jQuery](http://jquery.org/), [MooTools](http://mootools.net/), [YUI](http://yuilibrary.com/), [underscroe](https://github.com/documentcloud/underscore), [RequireJs](http://requirejs.org/), [SeaJs](http://seajs.org/docs/), [Wind.js](http://windjs.org/cn/)等各式各样的框架或者库出现在了大家的视野中。
笔者早期是从接触Prototype开始的，而后由于Prototype篡改原生对象的做法越来越受人诟病，才将其抛弃。而像underscore这种以第三方库的形式(占用一个全局变量)对JavaScript进行扩展的方法，则更能被人所接受。
总体来说，现在的第三方库与方法，足以支持日常工作的绝大部门需求。

然后比较令人失望的是，在经过了将近8个年头后，JavaScript语言的抽象能力却几乎没有得到任何提升，如今的语法还是当年的语法。这导致了笔者对姿态优雅的[CoffeeScript](http://coffeescript.org/)有着百般好感。

在上述的改进中，我们看到了大量的社区的身影。而作为JavaScript运行的主要平台，浏览器这一方，除了在提高执行速度外，还对语言本身也做了不少改进，这其中最具代表性的是Firefox。

接下来我们还是从上面列举的两个问题的角度来看看，从当年的v1.5，到如今的v1.8，Mozilla Firefox对JavaScript语言的改进做出了什么，或者又对语言做出了什么样的改进。


### Javascript 1.6 的改进

JavaScript 最早是由 Firefox 1.5 所支持的，这算是一个很早期的改进。基本上体现在对原生库与方法的改进上，而这其中的大部分又体现在对Array 的扩展上。如下：

* `indexOf()`
* `lastIndexOf()`
* `every()`:		所有数据都满足筛选条件
* `filter()`:		筛选出满足条件的记录集
* `forEach()`:		遍历记录集
* `map()`:			将记录集映射到另一个记录集
* `some()`:			是否至少有一条数据满足条件

通过下面的代码，可以看出这些方法并不难理解，而值得注意的是，这些方法也正是underscore扩展的主要部分。

{% highlight js %}
var arr = [1, 2, 3, 4, 5, 5, 6, 7, 8, 9, 10];

arr.indexOf(5); // 4
arr.lastIndexOf(5); // 5
arr.every(function(item) { return item > 0; } ); //true
arr.filter(function(item) { return item % 2 === 0; }); //[2, 4, 6, 8, 10]
arr.forEach(function(item) { });
arr.map(function(item) { return -1 * item; }); // [-1, -2, -3, -4, -5, -5, -6, -7, -8, -9, -10]
arr.some(function(item) { return item === 3; }); //true
{% endhighlight %}

在1.6版本中，另外还有一些对String与Array通用方法的扩展。比如String类型的toUpperCase()方法，一般来说我们需要先有String实例，才可以调用这个方法，或者是通过`String.prototype.call`来调用，在这次扩展中，部分方法被提升成了String/Array的静态方法。如下：

{% highlight js %}
var str = "abc",
	upper1 = str.toUpperCase(),
	upper2 = String.prototype.toUpperCase.call(str),
	upper3 = String.toUpperCase(str), // 改进处

	arr = [0, 1, 2],
	slice1 = arr.slice(1),
	slice2 = Array.prototype.slice.call(arr, 1),
	slice3 = Array.slice(arr, 1); // 改进处
{% endhighlight %}

具体被提升成静态方法的为String的
`quote`, `substring`, `toLowerCase`, `toUpperCase`, `charAt`, `charCodeAt`, `indexOf`, `lastIndexOf`, `startsWith`, `endsWith`, `trim`, `trimLeft`, `trimRight`, `toLocaleLowerCase`, `toLocaleUpperCase`, `localeCompare`, `match`, `search`, `replace`, `split`, `substr`, `concat`, `slice`, `fromCharCode`，
Array的 `join`, `reverse`, `sort`, `push`, `pop`, `shift`, `unshift`, `splice`, `concat`, `slice`, `indexOf`, `lastIndexOf`, `forEach`, `map`, `reduce`, `reduceRight`, `filter`, `some`, `every`, `isArray`等方法。

我们看到，在这个版本中，对提升语言抽象能力的改进不算太多，新增的方法勉强算是有一丁点吧。

### JavaScript 1.7 的改进

在1.7中，我们将看到更多地关于语言抽象能力的改进。

#### 生成器与迭代器 Generators and Iterators



#### Array comprehensions


#### let 关键字


#### 数组既元组


### JavaScript 1.8 的改进

### 结语