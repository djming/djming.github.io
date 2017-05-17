---
title: Android Studio更新后无法debug解决
date: 2017-05-17 12:02:17
tags:
	- 技术
	- Android
	- Android Studio
---

之前更新AS后遇到一个问题就是本来正常的项目突然在运行后无法自动调起界面了，我使用真机通过数据线连接到AS调试项目，点击运行后apk被正确安装到了手机上，但是没有在AS引导下自动打开app，并且debug也失败，无法调起debug界面。查了很多资料也没个结果，当时以为是AS新版本的问题，毕竟AS从1.5升级到2.0步子也挺大的，后来问学长却发现他没有遇到这个问题，依然跑得飞起。。。最后是一个已经工作的大神指导，让我升级**Gradle**版本试试，果然升级之后就OK了，这里记录一下升级过程。

<!--more-->

直接右键点击app文件夹，打开`Project Structure`，或者通过`File->Project Structure`打开，

![进入Project Structure](/imgs/path2ps.png)
进入到`Project`栏，在这里修改`Gradle`和`Android Plugin Version`的版本，
![修改版本](/imgs/change_gradle_version.png)
`Android Plugin Version`是**Gradle插件版本号**，这个也可以去`build.gradle`文件中直接修改。两者之间存在着对应关系，不能随便设置的，对应关系可以直接到[官方用户指南去查看](https://developer.android.com/studio/releases/gradle-plugin.html)

自从AS2.0版本以后经常在更新了AS之后打开项目时提示一个更新`INSTANT RUN`的通知，这个我每次都没看清就点确定了，今天更新的时候才注意到更新的通知栏其实就提示了`建议用户将Gradle升级3.3及插件2.3.2版本`，
![提示](/imgs/instant_run_update_dialog.png)
要是更新前仔细看也就不会那么痛苦地debug好几天了吧。。。