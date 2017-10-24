---
title: Kotlin学习笔记一
date: 2017-05-18 23:23:12
tags:
	- Kotlin
---

今天的Google开发者大会除了宣布Google的战略重点从移动端转移到AI领域外，另一向重点就是钦定了Kotlin为以后Android开发的第一语言。

Kotlin的大名我是早有耳闻，但是一直懒没有去学，而且当时Kotlin目前也仍处于发展中。如今有了Google的鼎力支持，再加上我最近颓废至极也该找点事做，于是去搜集了Kotlin的开发资料开始学习。

我使用的学习资料是不知谁翻译的[Kotlin官方文档中文版](https://www.gitbook.com/book/hltj/kotlin-reference-chinese/details)。第一步当然是从语法开始。但是如果你也是为了Android应用开发（目前Kotlin的主要应用领域），那么也许你也熟悉了Java语言，学习Kotlin就十分轻松了，我只记录下自己认为的重点。
<!--more-->
-
### Kotlin基本语法
- 不需要`;`作为句末
- ***var***与***val***的区别：Kotlin中所有的变量首先都是声明为***val***或者***var***，再赋值，若不赋值再指定类型。***val***为只读类型，***var***为可读写

``` kotlin
val a = 1   	// 直接赋值Kotlin会识别数据类型
var b : Int 	//Int首字母大写；指定b的数据类型为int

a = 2 		  	//运行出错，a值不可变

b = 1
b = 2			//修改b的值正常运行

```

- when的特殊使用
 
``` kotlin
val a = 1
when(a){
	1 -> println("a == 1")
	is Int -> println("a is int") 
	else -> println("unkown")	
}
//输出为 “a == 1” 
```
上面这段代码可以看出when替代了java中switch的作用，但是需要注意的一点是这里没有`break`，虽然*a*既等于1又是int但是输出判断出第一个条件正确后立即跳出，不会再继续向下判断。

- 几种区间比较:Kotlin中有几种表示范围的方法

``` kotlin
for(i in 1..10){...} 			//这种情况下循环中i会取1到10
for(i in 10..1){...}			//直接结束循环
for(i in 1..10 step 4){...}	//i = 1,5,9
for(i in 10 downTo 1){...}		//i取10到1
for(i in 10 downTo 1 step 4)	//i = 10,6,2
for(i in 1 until 10){...}		//i取1到9
```

- *break*与*continue*的使用：除了在Java中的普通用法，Kotlin还支持基于标签的用法

``` kotlin
a@ for(i in 1..3)		//标签为任意字符串+@ 如：loop@、abc@等
	b@ for(j in 1..3){
		println("$i + $j")
		if(i==2)
			continue@a	//注意这里continue与@之间无空格，执行后结束本次循环，进行下一次
		else if(j==2)
			break@b		//break与@之间也不需要空格，执行后直接结束循环，执行后面的代码
	}

/*
*1 + 1
*1 + 2
*2 + 1
*3 + 1
*3 + 2
*/

```

关于其他内容文档里面写得很仔细，我就不再赘述，这里是看文档时不太明白的地方通过编程测试后得出的结果。

-
###类与继承

- 类的主构造函数直接跟在类名之后

``` kotlin
/**你在Java里面这样写
*class Person{
*	Person(String firstName){}
*}
*但是在Kotlin中你可以这样写
*/
class Person constructor(firstName: String){}	//标准写法
class Person(firstName: String){}			      	//当主构造函数没有可见修饰符及注解时可省略 constructor 关键字
class Person(firstName: Sring)			      	//当类没有类体可省略花括号
```

- 主构造函数不能有代码，初始化操作要放入init代码块中

``` kotlin
class Person(String name){
	val personName = name          //也可以直接使用
	init(){
        println("name is $name")
	}
}

class Person(val personName: String){	//直接初始化属性
    init(){
        println("name is $name")
	}
}

```

- 创建类的实例不需要**new**关键字(Kotlin没有**new**关键字):

``` kotlin
    val p = Person("ming")
```

- 默认情况下，Kotlin中所有类默认均为**final**，只有声明了**open**才可以被继承

``` kotlin
open class Person(p : Int)
class Man(p : Int):Person(p)
```

- 类方法同样如此，只有标注了**open**才能被覆盖，并且除了覆盖，不能有与父类相同签名的函数

``` kotlin
open class Base {
    open fun v() {}
    fun nv() {}
}
class Derived() : Base() {
    final override fun v() {}   //override默认方法可以被子类覆盖，可加final标注禁止子类覆盖
}
```

- 属性覆盖与方法相似，但*var*可覆盖*val*，反之却不行

- 当父类与实现的接口有函数重名时的情况文档中写得很清楚。我突然想到Java中似乎没遇到类似的情况，首先应该是这种在设计上是不合理的，一般我们在编程时特意回避了。我测试了一下在Java中当类与接口函数重名时，子类仍然只能有一个该名称函数，并且必需是重写父类的，调用时无论转型为父类还是接口均是以该函数为准。若子类不重写该方法，则接口自动调用父类该方法。

``` java
class A{
    public void print(){
        System.out.println("A");
    }
}

interface B{
    void print();
}

class C extends A implements B{
    @Override                       //必须加上override注解
    public void print() {
        super.print();
        System.out.println("C");
    }
}

public static void main(String args){
    A a = new C();
    a.print();
    
    B b = new C();
    b.print();
}

/*输出均为：A
*         C
*若不重写print方法，则输出均为:A
*/
```
但是当重名函数返回类型不同时，编辑器会报错。

- **abstract**修饰的类和成员不用标注**open**

- Kotlin中没有静态方法。
