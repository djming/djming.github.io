### 自动装箱

``` java
Long sum = 0L;
long i = 0L;
sum += i;
```
上述代码中第三行`i`将被自动装箱为`Long`对象，从而消耗更多的资源。因此在普通场景下尽量使用基本类型，注意无意识的自动装箱。

### 组合与继承
1. 尽量使用组合，继承实际上破坏了封装性，父类的不良设计或者迭代修改都可能造成在一次更新后子类无法正常工作甚至无法通过编译。
2. 要么为继承而设计，要么禁止继承。
3. 构造器中不要调用可能被覆盖的方法。

### 接口优于继承
1. 接口使现有类很容易被更新。
2. Java中不允许多继承，但一个类可以实现多接口。接口也可以多继承。
3. 接口允许构造非层次结构的类型框架。
4. 尽量不要使用常量接口的形式（虽然这样会快很多），因为接口不能禁止自身被实现或继承，从而开发者不能保证使用者安全使用这些常量而不修改它们。

### 方法
1. 对于开发的方法做参数检查，将错误通过`throw`抛出，并在**JavaDoc**中编写`@throw`。
2. 对于未被导出的方法（即只能够被编写者自身调用），应该只传入有效方法参数，并通过`断言（assert）`检查，断言失败会抛出异常，成功则不会有成本开销：

``` java
private static void sort(long a[], int offset, int length) {
  assert a != null;
  assert offset >= 0 && offset <= a.length;
  assert length >= 0 && length <= a.length - offset;
  ...
}
```

3. 应该尽量设计通用的方法或构造器，对参数的限制越少越好。
4. 对不可变参数进行保护性拷贝,如果需要进行参数检查，应在保护性拷贝后对拷贝后的对象进行检查：

``` java
public final static Period {
  private final Date start;
  private final Date end;
  /**
  * public Period(Date start, Date end) {
  *   this.start = start;
  *   this.end = end;
  * }
  * 当在函数外部对传入的start对象调用如下方法时start将被改变:
  * start.setYear(78)
  **/
  public Period(Date start, Date end) {
    this.start = new Date(start.getTime);
    this.end = new Date(end.getTime);
  }
}
```
5. 不要过于追求提供便利以致类或接口臃肿、难以维护。
6. 避免过长的参数列表。
7. 慎用重载，注意类型被向上转型后编译时为父类型，因此不会调用以子类为参数的重载方法。同时自动装箱也会导致重载函数的调用超出预期，例如：`List<Integer>.remove(int i)`.

### 异常与错误
错误通常为JVM问题导致，如系统崩溃、动态链接失败等，不应由开发者捕获处理。


异常分为运行时异常（受检异常）和非运行时异常（非受检异常）。  
运行时异常如`NullPointException`等更应该属于编程错误，不推荐捕获处理，而应该在编程时避免异常出现。如果通过捕获异常来处理NullPoinException不仅造成JVM无法优化`try`代码块，也会对与后期维护造成困扰，并且可能吞掉本应被报出的其他异常。  
非运行时异常则应该被捕获处理（同时也是被编译器强制要求捕获），这类异常通常属于可补救回溯的异常，我们应该在`catch`代码块中还原对象。
