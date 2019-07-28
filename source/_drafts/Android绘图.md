---
title: Android绘图
reward: false
date: 2019-01-22 22:23:45
tags:
---
# Android图形绘制
-
### Paint

` setStyle(int mode)`传入的mode是枚举类型：

``` java
// Paint的内部枚举类
public enum Style {
        FILL            (0),            //会将画笔经过的区域闭环并填充颜色
        STROKE          (1),            //就是画线
        FILL_AND_STROKE (2);            //以上两种的结合
```
-
### Canvas

**save**与**restore**: 

>save之后对画布进行旋转等操作会导致坐标系变化,restore之后坐标系会回到save之前，从而保证我们的绘制操作始终在正确的坐标系下（如果不需要则不必调用这两个方法）

