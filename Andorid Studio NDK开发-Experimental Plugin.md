#Andorid Studio NDK开发-Experimental Plugin
gradle-experimental可以在不用编写.mk文件的情况下进行NDK开发。
做NDK开发有件比较麻烦的事情就是`Application.mk`和`Android.mk`的编写。而gradle-experimental就是用来解决这个麻烦的。
gradle-experimental是Android Studio的一个实验性的项目，基于gradle的一个插件，主要用来自动化NDK的配置实现，无需自己编写`Application.mk`和`Android.mk`，对于调试NDK项目也更加的友好。

环境要求：
* gradle>2.10
* Android NDK r10e
* Build Tools 19.0.0

##配置gradle-experimental

*  添加依赖
在项目的主目录下`./build.gradle`替换build tools,使用gradle-experimental
```
buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath "com.android.tools.build:gradle-experimental:0.7.0-alpha4"
    }
}
allprojects {
    repositories {
        jcenter()
    }
}
```

* 替换插件
在gradle中不能再使用`com.android.application`了，需要使用`com.android.model.application`替代。
在项目主目录下的`./app/build.gradle`中更改plugin
>apply plugin: "com.android.model.application"

* 配置model{}
```
model {
    android {
        compileSdkVersion 23
        buildToolsVersion "23.0.2"
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

##配置NDK
在配置NDK之前需要确认：

* NDK包是否下载
* NDK的环境变量是否配置

以上配置完成之后，在`./app/build.gradle`中配置android.ndk
```
model {

    android {
        compileSdkVersion 23
        buildToolsVersion "23.0.2"
        ndk{
            moduleName "experiment"
        }

    }

}
```
这里定义的`moduleName`就是后面生成的NDK的包名。

##源代码配置
默认情况下，我们在src/main/jni下使用的是c/c++文件。需要在model.android中指定：
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
分别指定了`java`源代码和`jni`源代码的目录。

##NDK代码
在java里面定义一个`native`方法,native标识的方法会需要在jni中实现，在java中调用。
```
public class NativeUtil {
 //加载ndk类库
    static {
        System.loadLibrary("experiment");
    }

    public static native String firstNative();
}
```
出现一个红色的警告：


![C8179238-12A2-4BA0-91FD-4F8DD08CA9F0.png](http://upload-images.jianshu.io/upload_images/22188-d670e77829788940.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以使用`alt+enter`键自动生成jni方法：

![65C7B949-105A-4E27-B550-F1278FE73F70.png](http://upload-images.jianshu.io/upload_images/22188-8f09bc990f9b780b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


生成的jni方法
```
**#include <jni.h>**

JNIEXPORT jstring JNICALL
Java_com_jjz_NativeUtil_firstNative(JNIEnv *env, jclass type) {

    // TODO

    return (*env)->NewStringUTF(env, returnValue);
}
```
修改jni方法返回的内容
```

JNIEXPORT jstring JNICALL
Java_com_jjz_NativeUtil_firstNative(JNIEnv **env, jclass type) {*
*    return (**env)->NewStringUTF(env, "is first native");
}
```
##运行
打开Toolbar中的运行配置菜单，可以看到:
![299F82BF-74DC-43A1-94E8-E6E11C0F31BE.png](http://upload-images.jianshu.io/upload_images/22188-b41772565845ae24.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
多出了一个`app-native`的菜单，可以直接运行。
调用NativeUtil.firstNative的方法,会得到jni中写好的返回值:
>is first native

可以看到使用`gradle-experimental`开发NDK,不用复杂的配置，自动化native方法，不需要定义.h头文件，对调试友好。

文中源代码地址:[https://github.com/jjz/android/tree/master/experimental](https://github.com/jjz/android/tree/master/experimental)



