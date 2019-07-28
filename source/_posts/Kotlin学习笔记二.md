---
title: Kotlin学习笔记二
date: 2017-05-21 10:43:29
tags:
    - Kotlin
---

### 属性和字段

##### 属性声明
官方文档有这样一段话

>其初始器（initializer）、getter 和 setter 都是可选的。属性类型如果可以从初始器 （或者从其 getter 返回值，如下文所示）中推断出来，也可以省略。
例如:

>``` kotlin
>var allByDefault: Int? // 错误：需要显式初始化器，隐含默认 getter 和 setter
>...
>val simple: Int? // 类型 Int、默认 getter、必须在构造函数中初始化
>...
>```
<!--more-->

附带的实例代码让我以为 `val simple: Int? ` 这种写法是正确的，**val**与**var**在声明中由于**Getter与Setter**的不同导致声明方式存在差异。但是实际上不是这样的。

在类的声明中以上两种写法均无法通过编译。

``` kotlin
class Person constructor(){
    var name: String?
    val age : Int?
    init{
        name = "名字"
        age = 7
    }
}
```

如果不为属性直接指定值`var name = "名字"`那么就要在`init`中进行初始化。

我们也可以为属性添加`lateinit`修饰符使空属性避免初始化

``` kotlin
class Person constructor(){
    lateinit var name : String
    
}
```

上面代码可以通过编译，但是这里的`p`不能是原生类型如`Int`，并且只能是`var`属性。

##### 幕后字段

`幕后字段`是中文文档的翻译，原文为`field`，但是在我的印象中这个单词应该一般认为是`域`，在案例代码的使用场景是`setter`函数中的赋值。

``` kotlin
class Person constructor(){
    var name = "老李"
        set(n){
            name = n
        }
    	
}
fun main(args: Array<String>) {
   	val p = Person();
    println(p.name)
    p.name = "老王"
    println(p.name)
}
```

上述代码是无法通过编译的，虽然我们指定了`name`为可变的`var`类型，但是`set`函数中无法直接引用`name`，因此我们将函数修改为：

``` kotlin
var name = "老李"
        set(n){
            field = n
        }
```

就可以运行了，当然如果不需要在`set`函数中实现特殊功能，我们可以不显示声明这个函数

``` kotlin
class Person constructor(){
    var name = "老李"
}
fun main(args: Array<String>) {
   	val p = Person();
    println(p.name)
    p.name = "老王"
    println(p.name)
}
```
上述代码也是完全没有问题的。

这里的不能引用仅仅是在属性的`SETTER`与`GETTER`函数中，类声明的函数是可以直接调用的：

``` kotlin
class Person(){
    var name = "老李"
    fun print(){
        println(name)
    }
}
```
我理解的`field`就是属性在`Getter`和`Setter`中对自身的引用，类似于Java中的`this`，我们在其他属性的`Setter`和`Stter`中依然可以直接引用属性:

``` kotlin
class Person constructor(){
    private var age = 12
    var getAge : Int
    	get() = age
    	set(a){
            age = a
        }
	var name = "老李"
    	set(name){
            field = name
        }
}
```

##### 可见性修饰符

Kotlin中的可见性修饰符与Java中稍有差异。Kotlin中不指定任何修饰符则默认为`public`，而`internal`为在同意模块中可见，与Java中的缺省修饰符类似。而`protected`虽然仍然存在却不推荐使用在顶层声明。

需要说明的一点是`private`的定义是在同一个文件下才可被访问，然而实际上仍然是在类中才可被访问，如果我们将下列代码写在同一`.kt`文件中仍然不能运行：

``` kotlin
class Person constructor(){
    private var name = "老李"
}
fun main(args: Array<String>) {
   	val p = Person();
    print(p.name)
}
```
这里也许是指的编译后的文件。

##### 扩展属性

关于扩展属性需要说明的一点是扩展属性没有`幕后字段`，所以扩展属性不能有`初始化器`，在`Setter`和`Getter`中也不能通过`field`来修改值，因此使用场景需要斟酌。

##### 密封类

密封类的含义看中文文档和代码完全没懂是什么意思。后来又找了些资料，大概理解了密封类是作为类似枚举的存在，密封类的每个子类都是枚举常量，可以通过`is 父类.子类`进行类型判断。

与普通枚举不同的是密封类的子类可以有多个实例，而枚举常量只有一个实例存在。

使用密封类的好处就是已知了子类的所有可能类型，便于进行判断处理。

##### 伴生对象

伴生对象可通过`类名.方法`进行调用，虽然看起来很像Java中类的静态成员，但实际上伴生对象仍然是类的实例的的实例成员。
