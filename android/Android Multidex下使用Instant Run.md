# Android 在Multidex下使用Instant Run
`Instant Run`是Android studio 2.0的新功能，在`Run`和`Debug`的时候，只有在第一次build会花费大量的时间。之后再次`Run`或者`Debug`的时候会直接更新到手机上面生成一个新的APK,这样就会很快的显示出变化，加快了开发效率。
`Instant Run`要求Gradle的版本在2.0.0以上，minSdkVersion为15，最好的minSdkVersion是21。随着Android studio 2.0正式版上线，想正式使用`Instant Run`功能，但是却遇到了一个问题：
```
Instant Run is disabled: Instant Run does not support deploying build variants with multidex enabled, to a target with API level 20 or below.  To use Instant Run with a multidex enabled build variant, deploy to a target with API level 21 or higher.
```
大意是开启了`multidex`，SDK的最低版本要是21才能使用`Instant Run`。为什么会有这个问题出现呢？

## Multidex
Android的Apk文件代码部分会代码成`Dalvik Exexutable`(DEX)文件，而每个DEX文件都是有方法数的限制的，不能超过65536个方法，包括使用的类库，和自己写的代码的方法数，65536又是64k，这个限制又称为64k限制，怎么解决这个解决这个限制。
	1.减少方法数
	2.使用多个DEX文件
使用多个DEX的方式，是在gradle里面开启：
```
android {
    compileSdkVersion 21
    buildToolsVersion "21.1.0"

    defaultConfig {
        ...
        minSdkVersion 17
        targetSdkVersion 23
        ...
        multiDexEnabled true
    }
    ...
}

```
导入multidex的依赖:
```
dependencies {
  compile 'com.android.support:multidex:1.0.0'
}
```
可以使用两种方式让Apk分包，使用`MultiDexApplication`：
```
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.android.jjz">
    <application
        ...
        android:name="android.support.multidex.MultiDexApplication">
        ...
    </application>
</manifest>
```
如果已经有了Application文件可以使用:
```
 @Override
    protected void attachBaseContext(Context base) {
        super.attachBaseContext(base);
        MultiDex.install(this);
    }
```
很遗憾的的是我们的APK依赖的库比较多，没有办法减少方法数到64K以下，错误提示里面说最小的SDK大于21（Android 5.0）以上是可以的，我们Android 5.0会不一样的呢？

## ART
Android runtime(ART)是管理runtime和系统服务的技术，ART和Dalvik能够从DEX的二进程文件中运行，ART天然地支持multiple DEX文件，ART在安装应用的时候扫描所有的DEX文件，编译成一个单独的.oat文件提供Android设备运行。更多的内容可以看:[ART](https://source.android.com/devices/tech/dalvik/index.html)

一方面想使用`Instant Run`的强大功能，另一方便又不能放弃4.0的Android设备。有没有一种能够两者兼具呢？
## Build Variant
不能每次调试的都去更新minSdkVersion,这样一是不方便，另外是容易出错，容易把错误的代码提交到版本库，导致不必要的错误出现，有没有一种方式能够通过配置构建出不同的环境，马上就能到gradle。
`productFlavors`是gradle中的一个功能，能够根据不通的定义构建不通的APK,比如构建不同渠道的渠道包，一般`productFlavors`的DSL是这样的:
```
android {
  
    productFlavors {
        flavor1 {
        }
        flavor2 {
        }
    }
}

```
`Build Variant` Build Type加上productFlavors就是Build Variant，比如一般的Build Type就两种`debug`和`release`。对应的Build Variant就是：
```
flavor1+debug
flavor1+release
flavor2+debug
flavor2+release
```
利用Build Variant可以定义不同的APK,可以设置不通的version，也可以设置不通的`minSdkVersion`。
首先我们使用`productFlavors`
```
android {
    productFlavors {
        instant {
            minSdkVersion 21
        }
        app {
            minSdkVersion 17
        }
    }
}
```
同步完gradle之后，可以在View->Tool Windows找到`Build Variants`：

可以看到对于的Build variant：

选择**appDebug**运行使用的`minSdkVersion`就是17,选择**instantDebug**运行使用的`minSdkVersion`就是21，就是使用`Instant Run`功能了。



