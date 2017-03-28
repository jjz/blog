#Andorid Studio NDK开发-Experimental Plugin
使用NDK开发有件比较麻烦的事情就是编写`Application.mk`和`Android.mk`的,而Android Studio 的插件`gradle-experimental`就是用来解决这个这个问题的。使用`gradle-experimental`插件可以不用再编写`*.mk`文件的情况下进行NDK开发。
`gradle-experimental`是Android Studio的一个实验性的项目，是基于gradle的一个插件，主要用来自动化NDK的配置实现，无需自己编写`Application.mk`和`Android.mk`，对于调试NDK项目也更加的友好，支持对于NDK的Debug。
下面就来尝试下`gradle-experimental`的便利吧！

## 环境要求：
* Android Studio > 2.0
* gradle>2.10
* Android NDK r10e
* Build Tools > 19.0.0

## 配置gradle-experimental

*  添加`gradle-experimental`依赖
在项目的主目录下`./build.gradle`中替换掉以前的`build tools`,使用最新版本的`gradle-experimental`
```
buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath "com.android.tools.build:gradle-experimental:0.7.0"
    }
}
```

* 替换插件(plugin)
使用`gradle-experimental`就不能再使用`com.android.application`了，需要使用`com.android.model.application`替代。
在项目主目录下的`./app/build.gradle`中更改`plugin`
>apply plugin: "com.android.model.application"

* 配置model{}

增加`model{}`,android的配置在`model{}`中。
```
   model {
          android {
        compileSdkVersion 23
        buildToolsVersion "25.0.0"
        ndk {
            moduleName "experiment"
            //stl = 'gnustl_static'
            //toolchain = 'clang'
            //todo 指定cpu
            abiFilters.addAll(['armeabi', 'armeabi-v7a']) //this is default
            ldLibs.addAll([ 'log']);
        }
        defaultConfig {
            applicationId "com.jjz"
            //需要使用.apiLevel
            minSdkVersion.apiLevel 15
            targetSdkVersion.apiLevel 23
            versionCode 1
            versionName "1.0"
        }
        buildTypes {
            release {
                minifyEnabled false
            }
        }
    }
}
```

## 配置NDK
在配置NDK之前需要确认：

* NDK包是否下载
* NDK的环境变量是否配置

能够正确运行命令：`ndk-build`。

以上配置完成之后，在`./app/build.gradle`中配置要配置`android.ndk`的相关内容：
```
model {
    android {
        compileSdkVersion 23
        buildToolsVersion "23.0.2"
        ndk{
             moduleName "experiment"
            //toolchain = 'clang'
            abiFilters.addAll(['armeabi', 'armeabi-v7a']) //this is default
            ldLibs.addAll([ 'log']);
        }

    }

}
```
这里定义的`moduleName`就是后面生成的NDK的包名。我们还可以指定其编译成对应的cpu，编译工具，使用的类库等。

## 源代码配置
默认情况下，在`src/main/jni`下使用的是c/c++文件。也可以在`model.android`中指定：
```
model {
    android {
        compileSdkVersion 23
        buildToolsVersion "23.0.2"
        ndk {
            moduleName "experiment"
        }
        sources {
             main {
                jni {
                    source {
                        srcDir "src/main/jni"
                    }
                }
                java{
                    source{
                        srcDir "src/main/java"
                    }
                }
            }
        }
    }
}
```
在`sources`分别指定了`java`源代码和`jni`源代码的目录。
以上的配置完成之后，就可以使用`gradle experimental`开始NDK开发了。

## 使用`gradle experimental`
在java里面定义一个`native`方法,native标识的方法会需要在jni中实现，可以在java中调用,还需要加载NDK生成的`.so`类库。
```
public class NativeUtil {
    //加载类库
    static {
        System.loadLibrary("experiment");
    }
    public static native String firstNative();
}
```
这个时候编译器会出现一个红色的警告，因为定义的`native`方法编译器并没有找到对应的实现：


![编译器警告没有实现native方法](http://upload-images.jianshu.io/upload_images/22188-d670e77829788940.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以使用`alt+enter`键可以自动生成jni文件：

![生成jni文件](http://upload-images.jianshu.io/upload_images/22188-8f09bc990f9b780b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


注意这个时候生成直接的是`experiment.c`,没有`.h`文件，生成的jni文件的内容:
```
**#include <jni.h>**
JNIEXPORT jstring JNICALL
Java_com_jjz_NativeUtil_firstNative(JNIEnv *env, jclass type) {
    // TODO
    return (*env)->NewStringUTF(env, returnValue);
}
```
自动生成的jni文件并没有具体的实现，需要修改jni文件让其返回一段测试内容：
```
>JNIEXPORT jstring JNICALL
Java_com_jjz_NativeUtil_firstNative(JNIEnv **env, jclass type) {
    char chars[] = "i am test";
    return (*env)->NewStringUTF(env, chars);
}
```
这样就完成了一个JNI开发调用，定义了一个`native`方法，在`.c`文件中对其进行了实现。如果没有`gradle experimental`插件的话，现在就需要使用`ndk-build`命令编译成`.so`文件，然后在运行Android项目,调用方法看下是否能够调用成功，而现在有了`gradle experimental`就变的了如此简单，下一步就需要直接`运行`就可以了，对！就是直接运行，省略了中间的`ndk-build`环节，就和编写Java代码一样，直接`运行`即可。

## 直接运行
打开`Toolbar`中的运行配置菜单，可以看到:
![app-native](http://upload-images.jianshu.io/upload_images/22188-b41772565845ae24.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
除了`app`以外，多出了一个`app-native`的菜单，这个选项可以直接编译NDK源码之后再运行`Android`，这样编写完`NDK`之后可以运行了，不需要再进行`ndk-build`:
在Java中调用`NativeUtil.firstNative`,会得到jni中写好的返回值:`i am test`,在这个例子中，我让这个这字段显示在主页面上。

可以看到使用`gradle-experimental`开发NDK,不需要再做复杂的配置，自动化native方法，不需要自定义.h头文件，对开发更加友好。

文中源代码地址:[https://github.com/jjz/android/tree/master/experimental](https://github.com/jjz/android/tree/master/experimental)
