---
layout: post
title: Android模拟器环境下SD卡内容的管理
---

本文旨在介绍一些Android模拟器下如何对SD卡内容进行管理的基本命令，同时也给大家推荐一个比较实用的工具。

笔者所用的环境：Windows 7 + Android SDK 2.2


### 1. 创建SD卡并挂载到模拟器

运行`cmd`，并`cd`至`android-sdk\tools\`目录下。输入如下命令则可创建并挂载SD卡到模拟器上。`mksdcard` 的参数中SD卡的大小单位可以为K或者M(必须为大写)，他会在`tools`目录创建一个文件叫`sdcard.img`。通过在emulator后添加 `-sdcard` 即可挂载SD卡并启动模拟器。

{% highlight bat %}
mksdcard 256M sdcard.img
emulator -avd 2.2 -sdcard sdcard.img
{% endhighlight %}


### 2. 操作单个文件

通过 `adb push`，则可将文件添加到SD卡中。如果想在push的时候修改文件名称的话，只需要修改push的第二个参数改成完整路径（目录+文件名），如 `/sdcard/test-0.jpg`。重启模拟器，则可以在见到SD卡中新添加的内容了。

{% highlight bat %}
adb push E:/images/test.jpg /sdcard/
adb push E:/images/test.jpg /sdcard/test-0.jpg
{% endhighlight %}

而要将test.jpg提取出来的话，可使用`adb pull`命令。第二个参数为本机硬盘地址，如果不想改名的话，只需要输入目录地址（不能加\)，而需要改名的话，输入文件的完整路径就行了。

{% highlight bat %}
adb pull /sdcard/images/test.jpg E:\tmp
adb pull /sdcard/images/test.jpg E:\tmp\test-0.jpg
{% endhighlight %}


### 3. 操作整个文件夹

要将硬盘上的一个文件夹的所有内容都添加至SD卡的话，可以通过如下命令（注意pic后面不能有\）：

{% highlight bat %}
adb push E:\Android\pic /sdcard/images/
{% endhighlight %}

![文件夹操作](/images/android-sdcard/dir-ops.png)

该命令会把pic文件下以及其子文件夹的内容全部添加到SD卡的images目录下。如果SD卡下没有images目录的话，可以先通过adb shell进行创建，如下：

{% highlight bat %}
adb shell
cd /sdcard
mkdir images
exit
{% endhighlight %}

而导出整个文件夹的话，可以通过如下命令：

{% highlight bat %}
adb pull /sdcard/images/ E:\tmp
{% endhighlight %}


### 4. WinImage - 一个实用的SD卡内容管理工具

觉得输命令比较麻烦的话，可以使用鄙人推荐的这款[WinImage](http://www.winimage.com/)。通过这款软件，我们可以像Windows Explorer一样方便的管理SD卡的内容，他的使用方法也相当简单，通过拖拽文件（夹）就可以将PC机硬盘上的内容添加到SD卡了。

需要注意的是，在模拟器启动的时候，会对SD卡进行锁定，在tools目录下会生成一个叫sdcard.img.lock的文件夹，这时候使用WinImage是不能打开sdcard.img文件的。

以下为截图：

![WinImage](/images/android-sdcard/WinImage.png)