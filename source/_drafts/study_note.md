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

### 疑问
  1. 创建reccyclerview的viewholder时若指定了viewgroup不为null，在自定义的layoutmanager如果调用addView会造成问题，这时通过removeView可以解决，但是这种情况会把viewholder也remove掉，原因需要查询。
