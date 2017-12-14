---
title: Kotlin学习笔记二
date: 2017-05-21 10:43:29
tags:
    - Kotlin
---

### 泛型
#### in与out
简单的说`List<in String>`等同于`List<? super String>`而`List<out String>`等同于`List<? extends String>`，而更深层则时斜变与逆变，[详解](http://ohmerhe.com/2015/12/22/class-object-generic/)
