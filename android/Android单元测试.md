# Android单元测试
Android的单元测试有两种方式:`本地单元测试`和`设备单元测试`，`本地单元测试`可以直接运行在本地机器上面的Java Virtual Machine(JVM)上面。它的特点是运行时间短，执行效率高，但是不依赖Android framework，每个文件都可以进行单独的测试。
运行在设备上的单元测试，运行的是一个单独的APK,直接运行到虚拟机或者设备上，这种单元测试拥有整个App的生命周期和运行环境。
这里主要介绍运行设备上的单元测试为主。

## 测试文件在哪？
下面是一个Android项目的基本结构：
![Android项目结构](http://upload-images.jianshu.io/upload_images/22188-f1af1fe48fa8d2bd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


其中`../app/src/test/java`目录下是**本地单元测试**的文件目录。
`../app/src/androidTest/java`目录下就是**设备单元测试**的文件目录。

## **配置测试环境**
首先需要引入单元测试的依赖库，在`AndroidManifest.xml`里面添加*uses-library*：
```
    <application
        .....
        <uses-library android:name="android.test.runner"/>
    </application>
```
配置单元测试框架的启动项：
```
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.jjz">

      <instrumentation android:name="android.test.InstrumentationTestRunner"
        android:targetPackage="com.jjz"
        android:label="test"/>

</manifest>
```
`android.test.InstrumentationTestRunner`是Android单元测试其中的一个启动项，用来启动单元测试。
`com.android.test.runner.MultiDexTestRunner`是另一个单元测试的启动项用来启动`MultiDex`的项目。
设置`android:targetPackage`与项目的`package`相同，这样当前单元测试的运行和应用程序是在一个进程里面。
## 编写测试代码
在项目中增加一个方法用来比较两个字符串的是否相等，在对该方法进行做单元测试:
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

增加方法`compare`的单元测试代码,在`../app/src/androidTest/java/com.jjz`添加文件`StringUtilTest.java`。
```

public class StringUtilTest extends AndroidTestCase {

    public void testCompare() {
        Assert.assertFalse(StringUtil.compare(null,"123"));
        Assert.assertTrue(StringUtil.compare("123","123"));
    }
}

```
需要注意的是类名必须使用`Test`结尾，需要继承`AndroidTestCase`，单元测试的方法需要使用`test`开头。
## 运行
在`../app/androidTest/java`文件夹上面，可以看到运行单元测试的按钮:
![Run all tests](http://upload-images.jianshu.io/upload_images/22188-1da40969c30d51bc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
直接点击`Run all tests `，可以看到运行单元测试的结果:

![测试结果](http://upload-images.jianshu.io/upload_images/22188-499d2fbd546081e3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



源代码地址：[https://github.com/jjz/android/tree/master/experimental](https://github.com/jjz/android/tree/master/experimental)




