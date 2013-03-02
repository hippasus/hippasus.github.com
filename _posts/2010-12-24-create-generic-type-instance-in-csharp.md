---
layout: post
title: C#中通过读取配置文件动态创建泛型对象
---

众所周知，在c#中，我们在获知的某一类型后，可以运用Activator.CreateInstance来动态的创建对象的实例。获取类型的话，可以使用typeof或者GetType来实现。而笔者最近遇到了一个需要通过读取配置文件来动态创建泛型类型的案例，让我们一起来看一下。


### 1. 背景

我们考虑一下这样的一个场景，我们需要开发了一个用于排序的通用的可扩展的库。利用该库，用户可以自定义排序规则(比较大小)以及需要进行排序的数据类型。而同时，我们开发了一个应用程序，在该应用程序中，我们可以让用户选择排序算法来进行排序。由于我们的排序库是可扩展的，因此，在开发应用程序的时候，我们并不知道会有多少种排序算法可供使用，因此我需要通过读取配置文件来动态的加载排序算法。


### 2. 排序库Sort.Lib

在Sort.Lib中，我们定义所有排序算法的通用接口，ISortAlgorithm

{% highlight c# %}
public interface ISortAlgorithm<T>
{
	void Sort(List<T> source, Func<T, T, int> compare);
}
{% endhighlight %}

比如我们有一个排序算法，叫HeapSort，注意该算法可能在Sort.Lib程序集中，也可能不在。


{% highlight c# %}
public class HeapSortAlgorithm<T> : ISortAlgorithm<T>
{
	public void Sort(List<T> source, Func<T, T, int> compare)
	{
		//TODO: sort the source
	}
}
{% endhighlight %}


### 3. 应用程序Sort.App

####3.1 动态创建排序算法

我们知道，要动态的创建HeapSortAlgorithm，只需要获取到其类型，然后利用Activator.CreateInstance就行。例如:


{% highlight c# %}
Activator.CreateInstance(Assembly.Load("assembly").GetType("full type name"));
{% endhighlight %}

而在此处，由于我们的HeapSortAlgorithm是个泛型类，则需要给泛型中的T赋予实际的类型。我们可以通过先获取泛型类型，再通过MakeGenericType来创建实际的类型。如下：


{% highlight c# %}
string assemblyName = string.Empty;
string fullTypeName = string.Empty;

//TODO: 读取配置文件，赋值给assemblyName和fullTypeName

Type[] typeArgs = { typeof(int) };

Type algTypeGen = Assembly.Load(assemblyName).GetType(fullTypeName);

//TODO: 判断algTypeGen是否实现了ISortAlgorithm<T>接口

// 获取HeapSortAlgorithm<int>类型
Type algType = algTypeGen.MakeGenericType(typeArgs);

HeapSortAlgorithm<int> heapSortAlg = Activator.CreateInstance(algType);
{% endhighlight %}


如此这般，我们的HeapSort算法就可以用上。

#### 3.2 配置文件

为了使我们的应用程序能够用上HeapSort，我们将其添加到我们的配置文件中去，如下

{% highlight xml %}
<SortAlgorithms>
	<Algorithm name="HeapSort" assembly="Sort.Lib" type="Sort.Lib.HeapSortAlgorithm`1" />
</SortAlgorithms>
{% endhighlight %}

注意到配置中type一项设置为<code>Sort.Lib.HeapSortAlgorithm`1</code>，这是因为泛型类`HeapSortAlgorithm<T>`在运行过程中，它的实际名称是<code>HeapSortAlgorithm`1</code>，这也可以通过`typeof(HeapSortAlgorithm<>)`得到。（注：关于这点，因为泛型参数只有一个T，所以会是 <code>HeapSortAlgorithm`1</code>）

另：如何去读取配置文件的代码在本文中我就不贴出来了。可以自己写ConfigurationSection类后来获取(推荐)，或者直接通过c#中读取XML文件的方式来实现。:)

#### 3.3 判断类型是否实现泛型接口

判断algTypeGen是否实现了`ISortAlgorithm<T>`接口，可以通过对algTypeGen实现的所有接口与`ISortAlgorithm<>`循环比对即可实现。


{% highlight c# %}
Type baseSortAlgType = typeof(ISortAlgorithm<>) 
bool isImplemented = algTypeGen.GetInterfaces().Any(x =>
	x.IsGenericType &&
	x.GetGenericTypeDefinition() == baseSortAlgType);
{% endhighlight %}


### 4. 后记

在本文中，我们了解了如何通过配置，实现动态创建泛型类型的实例。希望对大家有所帮助。