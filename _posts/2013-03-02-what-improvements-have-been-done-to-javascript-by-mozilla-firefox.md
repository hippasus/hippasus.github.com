---
layout: post
title: Mozilla Firefox 这些年为 JavaScript 的改进做了些什么？
---

### 序言

不得不承认，JavaScript算是比较弱的语言，主要表现为如下两个方面：

1. 原生的库与方法太弱，无法满足日常的需求
2. 抽象能力不是很强，影响产能

如果说不是因为Ajax的话，这门语言恐怕早已经被人遗忘，变成了一门没有个好的开始，也没能有善终的语言。

在JavaScript的春天刚开始的时候，它遵循是ECMA-262的第3个版本。Firefox上跑的是JavaScript 1.5, IE6上跑的还叫JScript，Chrome还未出生。

从那个时刻起，经过业界同僚的不断努力，JavaScript才真正慢慢变得强大了起来。而最先得到改善的还是体现在对库与方法的扩展上。[Prototype](http://prototypejs.org/), [jQuery](http://jquery.org/), [MooTools](http://mootools.net/), [YUI](http://yuilibrary.com/), [underscroe](https://github.com/documentcloud/underscore), [RequireJs](http://requirejs.org/), [SeaJs](http://seajs.org/docs/), [Wind.js](http://windjs.org/cn/)等各式各样的框架或者库出现在了大家的视野中。
笔者早期是从接触Prototype开始的，而后由于Prototype篡改原生对象的做法越来越受人诟病，才将其抛弃。而像underscore这种以第三方库的形式(占用一个全局变量)对JavaScript进行扩展的方法，则更能被人所接受。
总体来说，现在的第三方库与方法，足以支持日常工作的绝大部分需求。

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

#### 迭代器与生成器 Iterators and Generators

##### 迭代器 Iterators

我们知道在 JavaScript 1.5 中，很多对象是不支持迭代的，或者是使用`for...in`迭代的时候，行为会不尽如人意，甚至很根本无用。例如：
{% highlight js %}
var arr = [2, 4, 5, 6],
	obj = {
		"version": "1.7",
		"platform": "firefox",
		"standard": false
	};

for (var i in arr) {
	console.log(i); //0, 1, 2, 3 ！！！无用
}

for (var key in obj) {
	var v = obj[key]; // 需要通过obj再取一次值，不能把key-value pair一次性取出来
	console.log(v);
}
{% endhighlight %}

在这个版本中，通过使用`Iterator(obj[, indexOnly])`方法，我们则能得到更合理的迭代效果。`Iterator`方法返回一个`Iterator`对象，我们可以对它进行`next()`操作，`next()`操作默认返回一个键与值组成的数组。`Iterator`的第二个参数默认为`false`，如果为`true`的话，则表示只遍历`index`, 亦即 `Array`的索引或者`Object`的属性名称。
{% highlight js %}
var itArr1 = Iterator(arr),
	itObj1 = Iterator(obj),
	itArr2 = Iterator(arr, true),
	itObj2 = Iterator(obj, true);

console.log(itArr1.next()); // [0, 2]
console.log(itObj1.next()); // ["version": "1.7"]

console.log(itArr2.next()); // 0
console.log(itObj2.next()); // "version"
{% endhighlight %}

当所有元素迭代完后，继续调用`next()`方法，则会抛出`StopIteration`异常。
{% highlight js %}
var itObj = Iterator(obj);

try {
	console.log(itObj.next()); // ["version": "1.7"]
	console.log(itObj.next()); // ["platform": "firefox"]
	console.log(itObj.next()); // ["standard": false]
	console.log(itObj.next()); // throw StopIteration
} catch (err) {
	if(err instanceof StopIteration) {
		console.log("End of record.\n");
	}
}
{% endhighlight %}

除了使用`next()`之外，还可以针对iterator进行`for...in`操作。`for...in`内部会自动调用`next()`方法，并在遇到`StopIteration`时，遍历终止。
{% highlight js %}
for (var kv in Iterator(arr)) {
	console.log(kv); // kv 为索引值与具体值的组成的数组，如[0, 2]
}

for (var kv in Iterator(obj)) {
	console.log("Key: " + kv[0] + ",\tValue: " + kv[1]); // kv 为 obj 属性名与值缓存的数组
}
{% endhighlight %}

##### 自定义迭代器

现在假定我们有一个`Range`的数据结构，表示指定两个指定的数字left到right的区间。如果使用默认的`Iterator`方法构造迭代器的话，我们只能拿到它的left与right这两个属性与相应的值，而这不是我们所想要的。我们需要的是left与right之间的数集。
{% highlight js %}
function Range(left, right) {
	this.left = left;
	this.right = right;
}

var rng = new Range(1, 5);
for (var key in Iterator(rng)) {
	console.log(key); //["left", 1] & ["right", 5]
}
{% endhighlight %}

因此我们需要构造自定义的迭代器，这可以通过设置原型链中的`__iterator__`方法来实现。当然，根据我们上面的描述，迭代器有一个叫做`next()`的方法，并且在迭代完所有元素后继续被调用的情况下抛出`StopIteration`的异常。
{% highlight js %}
function RangeIterator(range){
	this.range = range;
	this.current = this.range.left;
}
RangeIterator.prototype.next = function(){
	if(this.current > this.range.right) {
		throw StopIteration;
	}

	return this.current++;
};
Range.prototype.__iterator__ = function(){
	return new RangeIterator(this);
};

var rng = new Range(1, 5);

var it = rng.__iterator__();
console.log(it.next()); // 1

for (var v in rng) { //直接对对象进行迭代，不再需要Iterator
	console.log(v); //1, 2, 3, 4, 5
}
{% endhighlight %}

我们可以知道，当使用`for...in`时，不再需要用使用`Iterator()`方法，`for...in`内部会去调用对象的`__iterator__()`方法拿到一个新的迭代器，并调用`next()`进行遍历。

##### 生成器 Generators

在上面我们看到，其实`RangeIterator`就是一个迭代器的生成器，简称生成器。在这一小节中，我们将看到Firefox给我们带来的一种更优的迭代器的生成器的做法--在语言层面引入对`yield`关键字的支持。

根据MDN[2]的解释，当一个依赖于`yield`的生成器被调用时，它内部的代码并没有（必须不能）被直接执行完(被`yield`给驻留了)，并会返回迭代器对象实例。同大多数现代语言中`yield`的用法一样，这时候程序的控制权被转交给了调用方。调用方负责调用返回结果的`next()`方法，`next()`方法会停留在下一个`yield`表达式处，并返回指定的值。当生成器方法执行到末尾，或者遇到了 `return` 语句时，`StopIteration` 会被自动抛出。因此，我们需要做的就是，给`__iterator__`设置成一个带`yield`语句的方法就行了。

> When a generator function is called the body of the function does not execute straight away; instead, it returns a generator-iterator object. Each call to the generator-iterator's next() method will execute the body of the function up to the next yield expression and return its result. When either the end of the function or a return statement is reached, a StopIteration exception is thrown.

{% highlight js %}
Range.prototype.__iterator__ = function(){
	for (var i = this.left; i <= this.right; i++) {
		yield i;
	}
};
{% endhighlight %}

从这部分我们看到，通过引入`yield`，JavaScript的迭代也变得相当方便并且实用起来。

#### Array comprehensions

在了解什么是Array comprehension之前，我们来看一下相应的代码：
{% highlight js %}
var nums = [1, 2, 3, 4, 5],
	doubled = [kv[1] * 2 for (kv in Iterator(nums))];
console.log(doubled); // [2, 4, 6, 8, 10]
{% endhighlight %}

“Pythonic!”，当看到这代码的时候，我脑海中出现的第一个词就是这个。如果你也了解Python的话，我想你一对会这种写法称赞不已！

{% highlight js %}
var nums = [1, 2, 3, 4, 5],
	doubledEvenNums = [kv[1] * 2 for (kv in Iterator(nums)) if (kv[1] % 2 === 0)];
console.log(doubledEvenNums); // [4, 8]
{% endhighlight %}

Pythonic！！！ 还支持 if 筛选！！！

由此，我们不难发觉，Firefox 为我们提供了一种类似Python一样的语法来写JavaScript，以支持从一个数组中快速构建出另一个数组来。而这种写法可以用1.6中的`filter()`或者`map()`一次、多次甚至是组合操作来完成。例如上述两段代码则可以通过如下代码来完成：
{% highlight js %}
var nums = [1, 2, 3, 4, 5],
	doubleFunc = function(item) { return item * 2; },
	isEventNumFilter = function(item) { return item % 2 === 0; },
	doubled = nums.map(doubleFunc),
	doubledEvenNums = nums.filter(isEventNumFilter).map(doubleFunc);

console.log(doubled);
console.log(doubledEvenNums);
{% endhighlight %}

在该语法中，由于要用到`for...in`进行迭代，故而，我们可以从任意可以进行迭代的对象中快速地创建出新数组，对比下述代码，我相信你更能相信这种写法有多便利(1行 vs. 6行代码)。

{% highlight js %}
var rng = new Range(1, 5),
	doubleFunc = function(item) { return item * 2; },
	isEventNumFilter = function(item) { return item % 2 === 0; }
	doubledEvenNumsQuickly = [doubleFunc(v) for (v in rng) if (isEventNumFilter(v))],
	doubledEvenNumsTraditional = [];

for(var v in rng) {
	if(isEventNumFilter(v)) {
		doubledEvenNumsTraditional.push(doubleFunc(v));
	}
}
{% endhighlight %}

#### let与区块内变量

按照MDN[3]中对`let`的解释，它允许你声明只在它所属区块(由一对花括号包含着的语句组成一个区块)、所属语句或者表达式中有效的变量。

>let allows you to declare variables, limiting its scope to the block, statement, or expression on which it is used. This is unlike the var keyword, which defines a variable globally, or locally to an entire function regardless of block scope.

使用`let`声明变量可以有效地解决区块变量与外层变量冲突的问题。通过比较下面的代码，我们就可以看出。

{% highlight js %}
g = "global";
(function() {
	var a = "function";
	if (true) {
		let a = "block"; // 仅在 if 内有效
		var b = "block"; // 在 function 内均有效
		let g = "block";
		let c = "block";

		console.log(a); // block
		console.log(b); // block
		console.log(g); // block
		console.log(c); // block
	}
	console.log(a); // function
	console.log(b); // block
	console.log(g); // global
	console.log(c); // 抛引用错误
}());
{% endhighlight %}

`let`可以通过`let(i=0){}`的形式声明变量`i`并保持该变量的作用域只在`let`区块内。

{% highlight js %}
(function() {
	var a = "function";
	let(a = "block") {
		console.log(a); // block
	}
	console.log(a); // function
}());
{% endhighlight %}

`let`也可以用在`for`语句中声明一个或都多个区块变量。但在`for`语句中`var`与`let`不能混用。

{% highlight js %}
(function() {
	for(var i = 0; i < 2; k++) {
	}
	console.log(i); //2

	for(let j = 0, k = 0; j < 2; j++) {
		console.log(j); //分别输出 0, 1
		console.log(k); // 0
	}
	console.log(j); //引用错误
}());

(function(){
	for(var m = 0, let n = 1; m < 2; m++) { // 语法错误
	}
}());
{% endhighlight %}

#### 数组即元组


### JavaScript 1.8 的改进

### 结语

[1]: https://developer.mozilla.org/en-US/docs/JavaScript/New_in_JavaScript/1.7#Generators_and_iterators_(merge_into_Iterators_and_Generators) "Generators and Iterators in JavaScript 1.7"
[2]: https://developer.mozilla.org/en-US/docs/JavaScript/Guide/Iterators_and_Generators#Generators.3A_a_better_way_to_build_Iterators "Generators: a better way to build Iterators"
[3]: https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Statements/let#Description "let description"
