#LeakCanary傻瓜式的内存泄露检测工具 在`Android`开发过程中如果需要处理图片或者大量数据的时候，常常会遇到**OOM**（`java.lang.OutOfMemoryError`）,一般出现最多的是在创建`Bitmap`上，也有可能是在内存中处理了大量的数据造成。 一般会针对Bitamp做下面几种的优化: 1. 增加进程的内存 2. 使用Bitmap.Config.ALPHA_8(图片失真) 3. 显示的调用System.gc() 4. catch Exception 5. 调用bitmap.recycle() 6. 缩小bitmap的大小（如果是读取的原图是一个大图应该先采用这种方式，Bitmap如果是刚好适配屏幕的就不需要缩小了） 7. 使用弱引用和软引用（google已经不建议使用了,Android的GC效率非常高，只要保证对象没有被引用即可） 但是我们会忽略掉一个问题就是什么造成了**OOM**？一般都发生**OOM**崩溃的地方都不一定是内存泄露的地方，崩溃了的原因可能`Activity`造成的内存泄露，也可能是操作数据库造成的内存泄露，当内存已经非常接近峰值的时候，这个时候恰巧要创建一个`Bitmap`对象就会发生OOM(Bitmap对象占用的内存空间比较大)。

## 内存泄露 每个对象都有自己的生命周期，`Activity`会调用`onDestroy`做销毁处理，但是如果使用`Activity`的`Context`调用`Toast`,就会把这个`Activity`的引用传给了`Toast`,而`Toast`的生命周期不会随着`Activity`的销毁而销毁,这样就造成了`Activity`的内存泄露，因为它被`Toast`引用着。 常见的内存泄露形成的原因: 1. Toast持有Activity的引用 2. 数据库游标Cursor没有关闭 3. Adapter没有复用convertView 4. 对象被生命周期更长的对象引用，Activity被静态集合引用 .... ## 监控内存的方式`Heap Dump`是一种Java比较常用的检测内存的方式，简单来说就是我们在一个初始状态*A*， Dump一次内存，在做了一些操作之后回到状态*A*，再Dump一次内存。 对两次Dunp的内存数据(`hprof`)使用分析工具做分析（`MAT`）,根据分析的结果就能知道是否存在内存泄露，这种方式比较复杂和繁琐并不是特别易用。`Moitors`:Android SDK 自带的内存监控工具，Monitors能看到内存的变化，内存是增加还是减少.打开一个Activity会导致内存增加，关闭一个Activity会导致内存减少，反复的实验如果每次打开一个Activity再关闭之后**增加的内存**不会减少就说明这个Activity有内存泄露的问题，可以使用log辅助进行检测，这种方式的缺点是并不是特别的准确，因为内存的释放和对象的生命周期有关也和GC的调用有关。

而`LeakCanary`就是一个简单的，方便的内存检测工具，可以轻易的发现内存问题，还会生成更加简单清晰的报告。

## LeakCanary`LeakCanary`是一个开源的检测内存泄露的java库。项目地址：https://github.com/square/leakcanary LeakCanary实际上就是在本机上自动做了Heap dump，对生成的`hprof`文件进行分析，展示结果。和手工分析`Heap Dump`的方式得到的结果是一样的。 下面是一个LeakCanary的结果截图：

![LeakCanary](http://upload-images.jianshu.io/upload_images/22188-c8327fc0f3ba73ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 集成LeakCanary 在`build.gradle`添加依赖：

```
dependencies {
   debugCompile 'com.squareup.leakcanary:leakcanary-android:1.3.1'
   releaseCompile 'com.squareup.leakcanary:leakcanary-android-no-op:1.3.1'
   testCompile 'com.squareup.leakcanary:leakcanary-android-no-op:1.3.1'
 }
```

使用LeakCanary会影响程序的性能，尤其是在Heap dump和分析操作时，不过我们可以在依赖里面指定对应的版本，debug的时候才进行分析，release的时候不进行分析。`debugCompile`可以使用检测版本：*com.squareup.leakcanary:leakcanary-android*

`releaseCompile`使用no-op模式，即`No Operation Performed`就是不会把对应的类库编译,指定类库为无用的指令:**com.squareup.leakcanary:leakcanary-android-no-op**设置`LeakCanary`为无用指令。 然后在`Application`中加入分析Activity的代码：

```
public class ExampleApplication extends Application {

  @Override public void onCreate() {
    super.onCreate();
    LeakCanary.install(this);
  }
}
```

这样就可以检测Activity的内存泄露了。内部实现使用了`ActivityLifecycleCallbacks`方法监控所有Activity的生命周期。

## 检测其他对象 LeakCanary中提供了`RefWatcher`类,可以用来监控所有的对象。 首先需要实例化`RefWatcher`:

```
public static RefWatcher sRefWatcher=LeakCanary.install(mContext);
```

对于监控的对象使用：

```
sRefWatcher.watch(this)
```

一般我们是在对象销毁的时候对对象进行监控,比如内部实现的对于Activity的监控:

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

只是在`onActivityDestroyed`的时候才对于activity进行了监控。

## 如何解决内存泄露 一般情况内存泄露的原因都是由于引用的使用不当造成的，而且Android的GC能够保证回收`循环引用`（如果一个循环引用没有外部引用时就会被回收)，而且Android的GC效率很高，当然GC的算法本身也在不停的改进。 一般情况下我们尽量避免错误的引用方式带来的内存泄露问题：

```
1. 生命周期长的对象引用生命周期短的对象，比如static的对象群引用Activity
2. 使用Application的Context对象，而不是Activity的Context
3. 避免非静态类的内部类对于类的隐式引用，使用静态的内部类
4. 使用Android的缓存机制，比如ListView的复用机制
5. 手动关闭资源，比如Curous的关闭
6. registerReceiver和unRegisterReceiver成对出现
```
