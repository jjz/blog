# Android 在Multidex下使用Instant Run
`Instant Run`是Android studio 2.0出现的新功能，在`Run`和`Debug`的时候，只有在第一次build会花费大量的时间。之后再次`Run`或者`Debug`的时候会直接更新到手机上面，这样就能很快的显示出变化，加快了开发效率。
`Instant Run`要求Gradle的版本在2.0.0以上，`minSdkVersion`最低为15，最好`minSdkVersion`在21之上。随着Android studio 2.0正式版上线，想正式开始使用`Instant Run`功能，但是却遇到了一个问题：
```
Instant Run is disabled: Instant Run does not support deploying build variants with multidex enabled, to a target with API level 20 or below.  To use Instant Run with a multidex enabled build variant, deploy to a target with API level 21 or higher.
```
大意是开启了`multidex`，SDK的最低版本要是21才能使用`Instant Run`。为什么会有这个问题出现呢？
要回答这个问题，先要了解下什么是`multidex`。

## Multidex
在`Android`平台中代码会编译成`Dalvik Executable`(DEX)文件，而DEX文件是有方法数的限制的，不能超过`65536`个方法，包括使用的类库和自己写的代码的方法总数。`65536`就是64k，所以这个限制又称为`64k限制`，怎么解决这个解决这个限制的问题呢？有两个方案
1. 减少方法数
2. 使用多个DEX文件（Multidex）

减少方法数固然是一个好方法，但是随着App功能的增加，控制一定的方式数是件很困难的事情，因此就需要使用`Multidex`的方案了。
使用`Multidex`，需要在gradle里面开启`Multidex`的设置，开启方式：
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
还需要导入`Multidex`的依赖:
```
dependencies {
  compile 'com.android.support:multidex:1.0.0'
}
```
可以使用两种方式让Apk分包。
* 使用`MultiDexApplication`：
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
* `Application`实现分包:
```
 @Override
    protected void attachBaseContext(Context base) {
        super.attachBaseContext(base);
        MultiDex.install(this);
    }
```

错误提示里面显示SDK大于21（Android 5.0）就可以，`Android 5.0`有不一样的呢？答案是`ART`。

## ART
`Android runtime(ART)`是管理runtime和系统服务的技术，ART和Dalvik都能够从DEX的二进程文件中运行，ART是天然地支持`Multiple DEX`文件，ART在安装应用的时候扫描所有的DEX文件，编译成一个单独的.oat文件提供Android设备运行。更多的内容可以看:[ART](https://source.android.com/devices/tech/dalvik/index.html)

在`Android 5.0`上全面支持了`ART`技术，因此可以方便的使用`Instant Run`,一方面想使用`Instant Run`的强大功能，另一方便又不能放弃低于5.0的Android设备。有没有一种能够两者兼具的方法呢？
## Build Variant
每次调试的都更新下`minSdkVersion`，这样一是使用不方便，另外是容易出错，容易把错误的代码提交到版本库，导致不必要的错误出现，有没有一种方式能够通过配置构建不同的环境，开发的时候使用`Instant Run`，Release的时候再支持`Android 5.0`以下的版本呢？这就要利用强大的gradle了。
要完成上面的任务需要使用一个功能`productFlavors`,productFlavors是gradle中的一个功能，可以根据不通的配置构建出不通的APK，比如构建不同渠道的渠道包时会用到，一般`productFlavors`的DSL是这样的:
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
还需要使用另一个就是`Build Variant`，* Build Type*加上*productFlavors*就是`Build Variant`。比如一般的Build Type有两种`debug`和`release`。对应的Build Variant就是：
```
flavor1+debug
flavor1+release
flavor2+debug
flavor2+release
```
利用`Build Variant`可以配置不同的APK，设置不通的`version`，也可以设置不通的`minSdkVersion`。
首先使用`productFlavors`:
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
同步完`gradle`之后，可以在View->Tool Windows找到`Build Variants`：
![Build Variants](http://upload-images.jianshu.io/upload_images/22188-4abdc93600a317e3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

对于的Build Variant：

![Build Variant](http://upload-images.jianshu.io/upload_images/22188-a0f5eef2ec9499e6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

选择**appDebug**就可以运行`minSdkVersion`等于17的Apk,选择**instantDebug**就可以运行`minSdkVersion`等于21的Apk，后者就是使用`Instant Run`功能了。

