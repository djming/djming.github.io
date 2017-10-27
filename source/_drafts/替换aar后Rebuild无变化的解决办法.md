---
title: 替换aar后Rebuild无变化的解决办法
reward: false
date: 2017-10-27 15:21:24
tags:
	- Android Studio
	- 小技巧
---

这几天写TV Launcher，使用公司自己的框架作为aar，在其之上进行开发，但由于框架完成时间较短，有很多设计不合理或不完善的地方需要进行修改，因此经常需要改完框架后再打包aar重新导入项目再完成需要。

最近的一个问题就是重新build了app-debug.arr导入新项目后再rebuild新项目会发现查看源码没有任何变化，被修改为public或者protected的方法子类仍然无法被访问，后来发现android studio时从一个叫做`/root/.android/build-chache/（Ubuntu系统下）`的目录下读取的aar源码，果断`rm -rf`清空了这个文件夹再刷新项目，就会发现这次连源码都没有了，满屏幕的红色下划线，再看`External libraried`目录下编译的依赖根本没有我们的aar文件。

最后解决方法其实很简单，先把aar依赖取消掉（不用删除`libs`目录下的aar文件），Rebuild一次，这时android studio会报错，提示找不到依赖，我们再去build.gradle中将依赖添加回来，再次编译，就OK了。

不知道是不是Android Studio 3.0的问题，这里记录一下。
