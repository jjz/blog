#Android Studio NDK开发-自定义库
项目中的公共代码或者公用模块，常常使用库的方式进行管理，除了第三方库以外，开发中也常常自己定义一个库。
在Android Studio使用`Modules`的方式让一个库的源代码在多个项目中使用，这是gradle在管理Android项目中常用的方式之一，那NDK的library库又是如何管理的呢?
`gradle-experimental`既然是gradle的扩展插件，必然有很多的地方和gradle类似，管理类库也是采用类似的方式。

##新建JNI类库
当前的`gradle-experimental`还不支持直接新建jni module，因此需要需要手动的去新建JNI类库。
首先把项目从`Android`切换到`Project`：

![project显示项目](http://upload-images.jianshu.io/upload_images/22188-da2ac2866adc2c47.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这样项目文件就显示为了树状结构：

![树状](http://upload-images.jianshu.io/upload_images/22188-f544ed11c86dfe7e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


新建目录`jni-lib`,在这个目录下面新建文件`bulid.gradle`，这样`jni-lib`目录就是一个gradle的module了。
在`sttings.grable`中把**jni-lib**，*include*进来
>include `:jni-lib`

接下来就需要构建一个基于`gradle-experimental`的NDK的 module了。
##com.android.model.native
插件`com.android.model.native`类似与`com.android.library`，是用来构建一个NDK类库的DSL。
在`./jni-lib/build.gradle`中添加代码:

```
apply plugin: 'com.android.model.native'

model {
    android {
        compileSdkVersion = 23
        buildToolsVersion = '23.0.2'

        defaultConfig {
            minSdkVersion.apiLevel = 14
            targetSdkVersion.apiLevel = 23
            versionCode = 1
            versionName = '1.0'
        }

        ndk {
            moduleName = 'lib'
            abiFilters.addAll(['armeabi', 'armeabi-v7a']) //this is default
            ldLibs.addAll(['android', 'log'])
        }

    }
```
`model.android.ndk`中定义ndk的属性:
```
model {
    android {
        ndk {
            moduleName = 'jni-lib'
            abiFilters.addAll(['armeabi', 'armeabi-v7a']) 
            ldLibs.addAll(['log'])
        }
    }
}
```
这里定义了类库的名字为:jni-lib,支持的平台`armeabi`和`armeabi-v7a`，调用系统的log库。

##JNI代码
JNI代码默认放在**module**的主目录下的`./src/main/jni`中，按照这个结构新建JNI代码目录。
JNI的库中需要定义.h文件，方便其他module直接引用，在`./src/main/jni`中新建llib.h文件
```
#include <android/log.h>

#define  LOG_TAG "jni-lib"
#define  LOGD(...) __android_log_print(ANDROID_LOG_DEBUG,LOG_TAG,__VA_ARGS__)

extern  void  callMethodFromJniLib();
```

新建llib.c文件实现llib.h中定义的方法：
```
#include "llib.h"

extern void callMethodFromJniLib() {
    LOGD("call methon from jni lib");
}
```

##引入**jni-lib**
调用jni-lib类库的时候，首先需要把`header`文件都给暴露出来，这样可以在其他的module中直接引用，`modle.android.sources`中指定需要暴露的文件:
```

model {
    android {
       ....
        sources {
            main {
                jni {
                    exportedHeaders {
                        srcDir "src/main/jni"
                    }
                }
            }
        }

    }
}
```
通过`exportedHeaders`这个DSL就可以块中指定需要暴露的文件。

其他module中引用`jni-lib`的就比较简单一些了，直接指定`jni`的依赖项即可,在`model.android.sources.main.jni`中指定依赖:
```

model {
    ....
    android {
       ...
        sources {
            main {
                jni {
                    dependencies{
                        library 'openssl' linkage 'static'
                        project ':jni-lib'
                    }
                   
                }
             }
        }

       ...

    }

}
```
`jni.dependencies`中指定了依赖project：jni-lib，`linkage`的默认值为`shared`。
这样就可以在app的jni中直接使用jni-lib中定义的方法了
```
#include <llib.h>

JNIEXPORT void JNICALL
Java_com_jjz_NativeUtil_callLogFromJni(JNIEnv *env, jclass type) {
    callMethodFromJniLib();//jni-lib中的方法
    __android_log_print(ANDROID_LOG_INFO, "jni-log", "from jni log");
    LOGW("log from  define");

}

```

源代码地址：https://github.com/jjz/android/tree/master/experimental

