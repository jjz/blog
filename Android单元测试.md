#Android单元测试
Android的单元测试有两种方式:`本地单元测试`和`设备单元测试`，`本地单元测试`直接运行在本地机器上面的Java Virtual Machine(JVM)上面。运行时间快，执行效率高，但是不能使用Android framework。因为不依赖Android framework每个文件都可以进行单独的测试。
运行在设备上面的单元测试，是一个单独的APK,直接运行到虚拟机或者设备上面，拥有整个App的生命周期和环境。这里主要以介绍运行再设备上面的单元测试为主。

##测试文件在哪？
下面是一个Android项目的基本结构：

其中`../app/src/test/java`目录下是**本地单元测试**的文件目录。
`../app/src/androidTest/java`目录下就是设备单元测试。

##配置测试环境
首先引入单元测试的依赖库，在`AndroidManifest.xml`添加：
```
<application
        .....
        <uses-library android:name="android.test.runner"/>
    </application>
```
配置短语测试框架的启动项：
```
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.jjz">

      <instrumentation android:name="android.test.InstrumentationTestRunner"
        android:targetPackage="com.jjz"
        android:label="test"/>

</manifest>
```
`android.test.InstrumentationTestRunner`是Android单元测试其中的一个启动项,`com.android.test.runner.MultiDexTestRunner`用来启动MultiDex的项目。设置`android:targetPackage`和项目的package相同，代表当前单元测试的运行和当前应用在一个进程中。
##编写测试代码
增加一个方法用来比较两个字符串的是否相等:
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

增加对于的测试代码,在`../app/src/androidTest/java/com.jjz`新增文件`StringUtilTest.java`。
```

public class StringUtilTest extends AndroidTestCase {


    public void testCompare() {
        Assert.assertFalse(StringUtil.compare(null,"123"));
        Assert.assertTrue(StringUtil.compare("123","123"));
    }
}

```
需要注意的是类名必须使用`Test`结尾，需要单元测试的方法需要使用`test`开头。
##运行
在`../app/androidTest/java`文件夹上面，可以看到运行单元测试的按钮:

直接点击`Run all tests `，可以看到运行单元测试的结果:

源代码地址：[https://github.com/jjz/android/tree/master/experimental](https://github.com/jjz/android/tree/master/experimental)





