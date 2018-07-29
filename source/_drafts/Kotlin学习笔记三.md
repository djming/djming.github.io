---
title: Kotlin学习笔记二
date: 2017-05-21 10:43:29
tags:
    - Kotlin
---

### 泛型
#### in与out
简单的说`List<in String>`等同于`List<? super String>`而`List<out String>`等同于`List<? extends String>`，而更深层则时斜变与逆变，[详解](http://ohmerhe.com/2015/12/22/class-object-generic/)

### 构造函数
#### 参数有无`val或var`的区别

``` Kotlin
class Sample(name : String) {
	init {
		print("Hello, $(name)") // 成功
	}
	fun hello() {
		print("Hello, $(name)") // 失败 
	}
}

class Smaple2(val name : String){
	init {
		print("Hello, $(name)") // 成功
	}
	fun hello() {
		print("Hello, $(name)") // 成功 
	}
}
```

究其原因是加上`val/var`后参数才会被编译成成员变量，而没有被修饰的参数则只是函数参数。
