# Android单元测试
Android的单元测试有两种方式:`本地单元测试`和`设备单元测试`，`本地单元测试`可以直接运行在本地机器上面的`Java Virtual Machine(JVM)`。它的特点是运行时间短，执行效率高，但是没有*Android framework*的支持，每个文件都可以进行单独的单元测试。
而在Android设备上的单元测试，运行的是一个单独的APK,直接运行到虚拟机或者物理Android设备上，这种单元测试拥有整个App的生命周期和运行环境。
这里主要介绍以运行在Android设备上的单元测试为主。

## 单元测试文件在哪里？
首先先看下单元测试文件是什么位置，下面是一个Android项目的基本结构：
![Android项目结构](http://upload-images.jianshu.io/upload_images/22188-f1af1fe48fa8d2bd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其中：
* `../app/src/test/java`目录下是**本地单元测试**的文件目录。
* `../app/src/androidTest/java`目录下就是**设备单元测试**的文件目录。

知道了单元测试文件的目录，下面就看下如何配置单元测试环境。
## 配置单元测试环境
需要使用单元测试,需要引入单元测试的依赖库，在`AndroidManifest.xml`里面添加*uses-library*：
```
    <application
        .....
        <uses-library android:name="android.test.runner"/>
    </application>
```
然后再配置单元测试框架的启动项：
```
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.jjz">

      <instrumentation android:name="android.test.InstrumentationTestRunner"
        android:targetPackage="com.jjz"
        android:label="test"/>

</manifest>
```
* `android.test.InstrumentationTestRunner`:是Android单元测试其中的一个启动项，用来启动单元测试。
* `com.android.test.runner.MultiDexTestRunner`:是另一个单元测试的启动项用来启动`MultiDex`的项目。

这里设置了`android:targetPackage`与项目的`package`相同，这样当前单元测试和应用程序会运行在同一个进程里面。
环境配置完成之后，就可以编写单元测试的代码了？
## 编写单元测试
为了使用单元测试方便，先在项目中增加一个方法，该方法用来比较两个字符串的是否相等:
```
public class StringUtil {
    public static boolean compare(String source, String other) {
        if (source == null) {
            return other == null;
        } else {
            return source.equals(other);
        }

    }
}
```
在对方法`compare`进行单元测试,在`../app/src/androidTest/java/com.jjz`添加文件`StringUtilTest.java`，文件内容为:
```
public class StringUtilTest extends AndroidTestCase {
    public void testCompare() {
        Assert.assertFalse(StringUtil.compare(null,"123"));
        Assert.assertTrue(StringUtil.compare("123","123"));
    }
}
```
需要注意的是类名必须使用`Test`结尾，并且要继承`AndroidTestCase`，而单方法必须使用`test`开头。
编写完单元测试之后，就可以运行了。
## 运行单元测试
在`../app/androidTest/java`文件夹上面，可以看到运行单元测试的按钮，如图所示:
![Run all tests](http://upload-images.jianshu.io/upload_images/22188-1da40969c30d51bc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
直接点击`Run all tests `，可以看到单元测试的结果:
![测试结果](http://upload-images.jianshu.io/upload_images/22188-499d2fbd546081e3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
这样就完成了一个简单的单元测试。

源代码地址：[https://github.com/jjz/android/tree/master/experimental](https://github.com/jjz/android/tree/master/experimental)

