# RxJava 2.0

## 简介

来自RxJava GitHub主页的自我介绍
> RxJava – Reactive Extensions for the JVM – a library for composing asynchronous and event-based programs using observable sequences for the Java VM.  
> 一个在 Java VM 上使用可观测的序列来组成异步的、基于事件的程序的库  

简单地说，RxJava是基于观察者模式的异步操作库，它有很方便的线程切换功能，可以以异步流的方式处理数据。
<br/>
<br/>
## 为什么使用RxJava

RxJava便捷的线程切换，赏心悦目的链式数据处理可以使我们保持高效的开发，并且保证代码始终拥有良好的可读性和可扩展性。
<br/>
<br/>
## 在Android项目中使用RxJava

直接在**build.gradle**文件中添加依赖：

``` gradle
compile 'io.reactivex.rxjava2:rxandroid:{VERSION}'
compile 'io.reactivex.rxjava2:rxjava:{VERSION}'
```
<br/>
<br/>
## 进入主题
在正式使用**RxJava**之前我们先来看看四个基本的概念：

- Observer——n.观察者
- Observable ——n.可观察者（被观察者）
- subscribe ——v.订阅
- event ——n.事件

这也是观察者模式中常被提到的四个概念，**Observer**与**Observable**通过**subscribe**关联起来后，**Observable**便可在适当的时候将事件发送给**Observer**，这看起来仍然是标准的观察者模式，我们来看一个简单的例子：  

``` java
Observable.just(floder)
                .subscribeOn(Schedulers.io())
                .flatMap(new Function<File, ObservableSource<File>>() {
                    @Override
                    public ObservableSource<File> apply(@NonNull File file) throws Exception {
                        return Observable.fromArray(file.listFiles());
                    }
                })
                .filter(new Predicate<File>() {
                    @Override
                    public boolean test(@NonNull File file) throws Exception {
                        return file.getName().endsWith(".png");
                    }
                })
                .map(new Function<File, Bitmap>() {
                    @Override
                    public Bitmap apply(@NonNull File file) throws Exception {
                        return getBitmapFromFile(file);;
                    }
                })
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(new Observer<Bitmap>() {
                    @Override
                    public void onSubscribe(Disposable d) {
                        
                    }

                    @Override
                    public void onNext(Bitmap bitmap) {
                        imageCollectorView.addImage(bitmap);
                    }

                    @Override
                    public void onError(Throwable e) {

                    }

                    @Override
                    public void onComplete() {

                    }
                });
```  
                
这段代码实现了从文件夹中遍历得到所有".png"图片并将其加载出来的全部过程。最终的Observer需要实现的是四个接口，相比与传统的观察者模式只能接受**onNext()**之外，还有**OnComplete()**与**OnError()**，而这两者是互斥的，他们将在**onNext()**不会再发出时被调用，通知订阅者队列已结束。

上面的例子虽然简短，但是几乎涵盖了RxJava开发中最常用的所有几个操作符，首先我们调用了**Observable.from()**创建出了一个**Observable**对象，接着便是一段行云流水般流畅的操作，值得一提地是最后一步的逻辑等同于`Obervable.subscribe(Observer)`，被观察者订阅了观察者，这种类似杂志订阅读者的逻辑虽然会让人稍难接受，但是RxJava的设计者们为了更好地保留流式Api的特性，只能在这里留下一点遗憾。

- **filter()**的作用是过滤队列，只有返回为**True**的消息会被继续发送，我们可以在**test()**中实现自己的过滤器逻辑，RxJava会做好剩下的事情。

- **flatMap()**与**map()**均是将接收到的消息转换为需要的其他形态再继续发送给链上的下一个处理器。而两者的区别从传出参数可以很明显地看到，**map()**将返回出一个普通的对象，而**flatMap()**会将被观察对象集合到一个**Observable**再发出，归纳出来，**map()**是一对一转换，而**flatMap()**是一对多转换，我们可以用**flatMap()**方法轻松地解决嵌套循环的问题。

除了以上几个最常用的操作符之外，RxJava还提供了许多的操作符让我们更方便地处理流，如:
`doOnNext()`，它将在`onNext()`被调用前被调用，类似的还有`doOnError()`，`doOnComplete()`，`doOnCancel()`等等。  

`take(int num)`将允许我们设定自己需要获取的数据量。比如电影我们只需要获取评分第一的，那么通过之前的操作符变换后在链上加入`.take(1)`即可。

RxJava提供了许多的便于我们处理数据的操作符，不再一一介绍，有兴趣的同学可以去[RxJava GitHub WiKi页面](https://github.com/ReactiveX/RxJava/wiki)查阅。

聊完了操作符，我们来说说RxJava最让我喜欢的功能——便捷地线程切换。
我们依然看上面的测试代码，在第2行和第21行是：

``` java
.subscribeOn(Schedulers.io())
.observeOn(AndroidSchedulers.mainThread())
```
这两句的就轻松完成了两次线程的切换，`subscribeOn(Scheduler.io())`指定事件将在`io线程`
被发出，`observeOn(AndroidSchedulers.mainThread())`则将接下来的操作转换到了主线程。更牛X的是，我们可以通过`observeOn()`随时切换线程。  
唯一需要注意的问题是`subscribeOn`只有第一次被调用时才有效，因为它指定的是事件被发出的线程，但它可以被写在整条链的任意位置，而`observeOn`只需要在需要切换线程的操作前被调用即可。

``` java
Observable.just(1,2,3,4)    //io线程，由第6行的subscribeOn指定
                .observeOn(Schedulers.computation())
                .map(mapper)    //切换到了computation线程
                .observeOn(Schedulers.newThread())
                .map(mapper)    //又切换到了新线程
                .subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(observer);   //回到了Android主线程，就是这么地自由。
```
在RxJava1和2中线程调度器稍有不同，不过这里介绍在2.0中最长使用的几种：

- Schedulers.computation() 线程数等于处理器数的线程池。
- Schedulers.io()   这是一个无上限但可复用的线程池，当线程不够时便创建，因此速度会比computation更快。
- Schedulers.newThread() 即创建新线程。
- AndroidSchedulers.mainThread() 即Android系统的UI线程，需要RxAndroid。

<br/>
<br/>
## 2.0 新特性
1. 增加了观察者模式
    - **Flowable(被观察者)/Subscriber(观察者)**
    - **Observable(被观察者)/Observer（观察者)**
    
    官方推荐在事件数少于1000时直接使用**Observable**作为观察者。
2. 通过**share()**使一个被观察者可以被多个观察者观察。

3. 增加了**onSubscribe()**方法

    ``` java
    public interface Subscriber<T> {
        public void onSubscribe(Subscription s);
        public void onNext(T t);
        public void onError(Throwable t);
        public void onComplete();
    }
    
    public interface Observer<T>() {
                public void onSubscribe(Disposable d) ;
                public void onNext(String s) ;
                public void onError(Throwable e) ;
                public void onComplete()
    }
    ``` 

4. 背压：通过**request(int taskNum)**获取事件。  

    ``` java
    Subscriber<String> subscriber = new Subscriber<String>() {
            Subscription subscription;
            @Override
            public void onSubscribe(Subscription s) {
                subscription = s;
                //请求一个数据
                subscription.request(1);
            }
    
            @Override
            public void onNext(String s) {
                Log.i(TAG, "onNext: " + s);
                //处理完后，再请求一个数据
                subscription.request(1);
            }
    
            @Override
            public void onError(Throwable e) {
                //异常处理
            }
    
            @Override
            public void onComplete() {
                subscription.cancel();
            }
        };
        flowable.subscribe(subscriber);
    }
```
