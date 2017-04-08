#LeakCanary傻瓜式的内存泄露检测工具
在开发`Android`应用的过程中如果需要处理图片或者大量数据的时候，就有可能会遇到**OOM**（`java.lang.OutOfMemoryError`）,一般出现最多的是在创建`Bitmap`上，也有可能是在内存中处理了大量的数据上。出现**OOM**应用会直接崩溃，即使没有出现**OOM**,内存使用过大的时候应用也会出现卡顿。所以内存的优化在开发Android应用时是一个比较重要的任务。
一般会针对`Bitamp`的内存优化有下面几种方式:
	1. 增加进程的内存
	2. 使用Bitmap.Config.ALPHA_8(图片失真)
	3. 显示的调用System.gc()
	4. catch Exception
	5. 调用bitmap.recycle()
	6. 缩小bitmap的大小（如果是读取的原图是一个大图应该先采用这种方式，Bitmap如果是刚好适配屏幕的就不需要缩小了）
	7. 使用弱引用和软引用（google已经不建议使用了,Android的GC效率非常高，只要保证对象没有被引用即可）
但是我们忽略掉一个问题就是什么造成了**OOM**？
一般来说发生**OOM**崩溃的地方不一定是内存泄露的地方，崩溃的原因有可能是`Activity`造成的内存泄露，也可能是操作数据库造成的内存泄露，当内存已经非常接近峰值的时候，这个时候恰巧要创建一个`Bitmap`对象就会发生`OOM`(Bitmap对象占用的内存比较大)。
是什么原因造成了内存泄露呢？
## 内存泄露
我们知道Android中每个对象都有自己的生命周期，比如`Activity`的生命周期最后会调用`onDestroy`方法做销毁处理，但如果使用`Activity`中调用了类似于`Toast`这种对象,就会把这个`Activity`的引用传给了`Toast`,而`Toast`的生命周期不会随着`Activity`的销毁而销毁,这样就造成了`Activity`的内存泄露，它会被`Toast`对象引用，无法被销毁。
常见的内存泄露形成的原因:

*  Toast持有Activity的引用
*  数据库游标Cursor没有关闭
* Adapter没有复用convertView
*  对象被生命周期更长的对象引用，Activity被静态集合引用
* ....

那如何知道应用的内存有没有出现泄露呢？
## 监控内存的方式

`Heap Dump`：常见的内存监控方式是`Heap Dump`，`Heap Dump`是一种在Java中比较常用的检测内存的方式：
>简单来说就是我们在一个初始状态**A**， 在这个时候Dump一次内存，在做了一些操作之后回到状态**A**，再Dump一次内存。
对两次Dunp的内存数据(`hprof`)使用分析工具做分析（`MAT`）,根据分析的结果就能知道是否存在内存泄露，这种方式比较复杂和繁琐并不是特别易用。

`Moitors`:这是`Android SDK `自带的内存监控工具，`Monitors`能检测到内存的变化，比如内存是增加还是减少。
打开一个Activity会导致内存增加，关闭一个Activity会导致内存减少，反复的做这样的操作，如果每次打开一个Activity再关闭之后**增加的内存**不会减少就说明这个Activity有可能有内存泄露，再借助`log`辅助进行检测，就可以发现内存泄露的问题，
这种方式的缺点是并不是特别的准确，因为内存的释放和对象的生命周期有关也和`GC`的调度有关。
另一种方式就是`LeakCanary`,`LeakCanary`是一个简单的，方便的内存检测工具，可以轻易的发现内存问题，还会生成更加简单清晰的报告。

## LeakCanary
`LeakCanary`是一个开源的检测内存泄露的java库。项目地址：https://github.com/square/leakcanary
LeakCanary实际上就是在本机上自动做了`Heap dump`，对生成的`hprof`文件进行分析，展示分析的结果。和手工分析`Heap Dump`的方式得到的结果是一样的。只不过这部分的工作完全自动化完成了。
下面是一个LeakCanary的结果截图：

![LeakCanary](http://upload-images.jianshu.io/upload_images/22188-c8327fc0f3ba73ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从上图可以看到，`LeakCanary`能清晰简单的展示出那里有内存泄露的问题。那`LeakCanary`如何使用呢？
## 集成LeakCanary
在`build.gradle`添加依赖：
```gradle
dependencies {
   debugCompile 'com.squareup.leakcanary:leakcanary-android:1.3.1'
   releaseCompile 'com.squareup.leakcanary:leakcanary-android-no-op:1.3.1'
   testCompile 'com.squareup.leakcanary:leakcanary-android-no-op:1.3.1'
 }
```
使用`LeakCanary`对应用进行检测它会影响程序的性能，尤其是在做`Heap dump`和`分析`操作时，因此需要在依赖里面指定对应的版本，**debug**的时候才进行分析，**release**的时候不能进行分析。
`debugCompile`可以使用检测版本：
>com.squareup.leakcanary:leakcanary-android

`releaseCompile`使用no-op模式，即`No Operation Performed`就是不会把对应的类库编译,指定类库为无用的指令:
>com.squareup.leakcanary:leakcanary-android-no-op

这样就可以指定`LeakCanary`为无用指令，不会在release的时候进行编译。
在`Application`中加入分析Activity的代码：
```java
public class ExampleApplication extends Application {

  @Override public void onCreate() {
    super.onCreate();
    LeakCanary.install(this);
  }
}
```
这样就可以检测所有`Activity`的内存泄露了。`LeakCanary`内部实现使用了`ActivityLifecycleCallbacks`方法监听所有Activity的生命周期。
除了`Activity`会发生内存泄露以外，其他对象也有可能会出现内存泄露，如果对其他对象进行检测呢？
## 检测其他对象
`LeakCanary`中提供了`RefWatcher`类,可以用来监控所有的对象。
首先实例化`RefWatcher`:
```java
public static RefWatcher sRefWatcher=LeakCanary.install(mContext);
```
对于监控的对象使用：
```
sRefWatcher.watch(this)
```
一般我们是在对象销毁的时候对对象进行监控,比如内部实现的对于Activity的监控的原理如下:
```java
private final ActivityLifecycleCallbacks lifecycleCallbacks = new ActivityLifecycleCallbacks() {
        public void onActivityCreated(Activity activity, Bundle savedInstanceState) {
        }

        public void onActivityStarted(Activity activity) {
        }

        public void onActivityResumed(Activity activity) {
        }

        public void onActivityPaused(Activity activity) {
        }

        public void onActivityStopped(Activity activity) {
        }

        public void onActivitySaveInstanceState(Activity activity, Bundle outState) {
        }

        public void onActivityDestroyed(Activity activity) {
            ActivityRefWatcher.this.onActivityDestroyed(activity);
        }
    };
```
只是在`onActivityDestroyed`的时候才对于activity进行监控即可。
检测到了内存泄露，如果解决呢？
## 解决内存泄露
一般情况内存泄露的原因都是由于引用的使用不当造成的，`Android GC`能够保证回收`循环引用`（如果一个循环引用没有外部引用时就会被回收)，且`Android GC`效率很高，当然GC的算法本身也在不停的改进。
一般情况下只需要尽量避免错误的引用方式带来的内存泄露问题即可：

*  生命周期长的对象引用生命周期短的对象，比如static的对象群引用Activity
* 使用Application的Context对象，而不是Activity的Context
*  避免非静态类的内部类对于类的隐式引用，使用静态的内部类
*  使用Android的缓存机制，比如ListView的复用机制
* 手动关闭资源，比如Curous的关闭
*  registerReceiver和unRegisterReceiver成对出现

