#LeakCanary傻瓜式的内存泄露检测工具
在`Android`开发过程中常常会遇到**OOM**（`java.lang.OutOfMemoryError`）,一般出现最多的是在创建Bitmap上，也有可能是在内存中处理了大量的数据造成的。
一般会针对Bitamp做下面几种的优化:
	1.增加进程的内存
	2.使用Bitmap.Config.ALPHA_8(图片失真)
	3.显示的调用System.gc()
	4.catch Exception
	5.调用bitmap.recycle()
	6.缩小bitmap的大小（如果是读取大图这样做是应该的，Bitmap如果是刚好适配屏幕的就不应该缩小）
	7.使用弱引用和软引用
一般我们会忽略掉一个问题就是造成OOM的原因一般都是不是发生**OOM**崩溃的地方，可能Activity造成的内存泄露，也可能是操作数据库造成的内存泄露，当内存已经非常接近峰值的时候，这个时候恰巧要创建一个Bitmap对象就会发生OOM，一般来说Bitmap对象占用的内存空间比较大。

##内存泄露
每个对象都有自己的生命周期，Activity会调用onDestroy做销毁处理，但是如果使用Activity的Context调用Toast,就会把这个Activity的引用传给了Toast,Toast的生命周期不会随着Activity的销毁而销毁,这样Activity会发生内存泄露，因为被Toast引用。
常见的内存泄露形成的原因:
	1.Toast持有Activity的引用
	2.数据库游标Cursor没有关闭
	3.Adapter没有复用convertView
	4.对象被生命周期更长的对象引用，Activity被静态集合引用
	....
##监控内存的方式
`Heap Dump`是一种Java比较常用的检测内存的方式，简单来说就是我们在一个初始状态A， Dump一次内存，在做了一些操作之后回到状态A，再Dump一次内存。然后使用分析工具做分析（MAT）,根据分析的结果就能知道是否存在内存泄露，这种方式比较复杂和繁琐并不是特别易用。
`Moitors`:Android SDK 自带的内存监控工具，Monitors能看到内存的变化，内存是增加还是减少.打开一个Activity会导致内存增加，关闭一个Activity会导致内存减少，反复的实验如果每次打开一个Activity再关闭之后增加的内存不会减少就说明这个Activity有内存泄露的问题，可以使用log辅助进行检测，这种方式的缺点是并不是特别的准确，因为内存的释放是内生命周期有关也和GC的调用有关。

而LeakCanary就是一个简单的，方便的内存检测工具，可以轻易的发现内存问题，还有更加简单清晰的报告。

##LeakCanary
LeakCanary是一个开源的检测内存泄露的java库。项目地址：https://github.com/square/leakcanary
LeakCanary实际上就是在本机上自动做了Heap dump，对生成的hprof文件分析，展示结果。和手工分析Heap Dump的方式一样。
下面是一个LeakCanary的结果截图：
![图片描述][1]

##集成LeakCanary
在`build.gradle`添加依赖：
```
dependencies {
   debugCompile 'com.squareup.leakcanary:leakcanary-android:1.3.1'
   releaseCompile 'com.squareup.leakcanary:leakcanary-android-no-op:1.3.1'
   testCompile 'com.squareup.leakcanary:leakcanary-android-no-op:1.3.1'
 }
```
使用LeakCanary会影响程序的性能，尤其是在Heap dump和分析操作时，不过我们可以在依赖里面指定对应的版本，debug的时候才进行分析，release的时候不进行分析。
`debugCompile`可以使用检测版本：*com.squareup.leakcanary:leakcanary-android*

`releaseCompile`使用no-op模式，即`No Operation Performed`就是不会把对应的类库编译,指定类库为无用的指令:*com.squareup.leakcanary:leakcanary-android-no-op*。
然后在`Application`中加入分析Activity的代码：
```
public class ExampleApplication extends Application {

  @Override public void onCreate() {
    super.onCreate();
    LeakCanary.install(this);
  }
}
```
这样就可以检测Activity的内存泄露了。内部实现使用了`ActivityLifecycleCallbacks`方法监控所有Activity的生命周期。

##检测其他的
LeakCanary中提供了`RefWatcher`,可以用来监控所有的对象。
首先需要实例化`RefWatcher`:
```
public static RefWatcher sRefWatcher=LeakCanary.install(mContext);
```
对于监控的对象使用：
```
sRefWatcher.watch(this)
```
一般我们是对对象销毁的时候对对象进行监控,比如内部实现的对于Activity的监控:
```
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
只是在`onActivityDestroyed`的时候才对于activity进行监控。

##如何解决内存泄露
一般情况内存泄露的原因都是由于引用的使用不当造成的，而且Android的GC能够保证回收`循环引用`,如果一个循环引用没有外部引用时就会被回收，而且Android的GC效率很高，（当然GC的算法本身也在不停的改进）。
一般情况下我们尽量避免错误的引用方式带来的内存泄露问题：

	1.生命周期长的对象引用生命周期短的对象，比如static的对象群引用Activity
	2.使用Application的Context对象，而不是Activity的Context
	3.避免非静态类的内部类对于类的隐式引用，使用静态的内部类
	4.使用Android的缓存机制，比如ListView的复用机制
	5.手动关闭资源，比如Curous的关闭
	6.registerReceiver和unRegisterReceiver成对出现


  [1]: /img/bVuPy4


