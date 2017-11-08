### HandleThread

自身维护了一个Looper的Thread，在创建时通过wait()阻塞消息队列知道Looper创建成功。

### IntentService
使用HandleThread实现的Service，默认不在UI线程，接受Intent投递信息，Intent队列处理完成后
结束生命周期。抽象类，需要继承使用。

### Executor 线程池
基础有三种类型，固定、可变、单线程池。

### AsyncTask
默认在后台单线程执行。

### Serializable
使用反射，序列化过程缓慢，易触发垃圾回收。对象的序列化基于字节。
基于磁盘或网络。

### Parcelable
Android SDk提供，基于内存，实现复杂。

### Protocol 与 Nano-Protocol—Buffers；FlatBuffers
有时间了解下

### android中JS调用Java
JS中：

``` Javascript
  <input type="button" value="打印"
   onclick="javaObject.printLog('From js;');"
```

android中：

``` java
public class JavaObject{
  public JavaObject(){}
  @JavascriptInterface
  public void printLog(String log){
    Log.d(TAG, log);
  }
}

WebSettings webSettings = mWebView.getSettings();
webSettings.setJavaScriptEnable(true);
mWebView.addJavascriptInterface(new JavaObject(), "javaObject");
```

android 4.2以前无@JavascriptInterface注解，导致webview远程代码执行漏洞。


### Android数据库
Realm：跨平台，非原生，更快，体积大
greenDAO:基于Android，轻量。

### 代码优化

1. 使用SparseArray取代HashMap。SparseArray线程不安全，基于二分查找。
2. 虚引用相当于没有引用，意思是虚引用不对被修饰的对象产生影响，仅作为标记，GC在回收被虚应用标记的对象后会将其放入引用队列。

### Java GC
缓存分为新生区与老年区，新生区分为一个伊甸区和两个幸存区，绝大多数被创建后放在伊甸区，经过一次GC后存活的移到幸存区，几次迭代后幸存区存活的移到老年区。
复制算法：速度快，需要大量内存。
标记-清除法：遍历导致速度慢，需要空间小，会有内存碎片。
标记压缩算法：遍历导致速度缓慢，无内存碎片。

存活判断：引用计数，可达性。

### 工具
1. lint
2. leakcanry
3. FaceBook Redex 压缩优化apk

### Looper、Message、MessageQeue
1. 通过Message.obtain()创建Message对象时不会直接新建，而是判断池中是否有空闲Message对象可复用。
2. Handler中通过dispatchMessage分发消息，若message自身带有callback则直接运行Message自带的callback逻辑，否则才运行Handler的callback逻辑。
3. Looper的使用需要先调用Looper.preper()创建线程对应的Looper并存入ThreadLocal中，再通过Looper.loop()开始死循环。
4. Handler中的MessageQueue是有该线程的Looper创建，是Looper中定义的成员变量。
4. 由于Handler可以被各个线程调用执行handleMessage的方法插入Message，但取出message是由Looper.loop()完成的，这个死循环运行在创建它的线程，因此达到了线程间传递数据的任务，但任务最终只能在创建Handler的线程运行。

### StartActivityForResult
当启动Activity为singleInstance时该activity会先结束自身再调起新的activity，也是就是onActivityResult会在新Activity调起前执行，resultCode为0.猜测是因为singleInstance的activity由于单独存在与一个Task，因此需要先弹出自身，切换Task才能调起新的activity，在使用时应当注意。

### View生命周期与Activity生命周期
如图![进入Project Structure](/imgs/view&activity%20life%20circle.png)

### RecyclerView 问题
1. RecyclerView的child会在onLayout时才加入进去，因此在上图中onLayout之前调用getChildAt(pos)会得到null，并且在重新绘制时由于child会被清理，因此也会得到null。
可以通过添加reccyclerview.addOnLayoutChangeListener()来监听onLayout事件第一时间获取child，如果只需要获取一次则记得在重写的方法中remove掉listener以节约资源。
2. RecyclerView只会保存当前显示在屏幕上的item，假如我们有100条数据，但屏幕只能看到1-20条，则`rv.getChildCount()//返回20（滑动时可能返回21）`，并且在调用`getChildAt(i)//i>21`时会得到null
但是`rv.smoothToPosition(i)`不会受到影响，因为recyclerview内部会自动计算滑动距离，与item对象是否存在无关。
3. 实际上`getChildAt(i)`获取到的是屏幕上可见的第一个item。

### 疑问
  1. 创建reccyclerview的viewholder时若指定了viewgroup不为null，在自定义的layoutmanager如果调用addView会造成问题，这时通过removeView可以解决，但是这种情况会把viewholder也remove掉，原因需要查询。
