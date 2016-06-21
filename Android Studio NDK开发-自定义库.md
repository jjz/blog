：
#Android Studio NDK开发-自定义库
公共部分的代码或者是公用模块的代码，常常使用库的方式管理，定义一个库,用`Modules`的方式在多个项目中使用，是gradle在管理Android项目中常用的方式，那么NDK的library库又是如何管理的呢?`gradle-experimental`既然是gradle的扩展插件，必然有很多的地方和gradle类似，管理类库也是采用类似的方式。

##新建JNI类库
当前的`gradle-experimental`还不支持直接新建JNI module，因此需要需要手动的去新建JNI类库：
首先把项目从`Android`切换到`Project`，让文件显示为树状结构：

新建目录`jni-lib`,新建文件`bulid.gradle`。
在`sttings.grable`中把jni-lib，include进来
>include `:jni-lib`
接下来就需要构建一个基于`gradle-experimental`的jni module了。
##com.android.model.native
插件`com.android.model.native`类似与`com.android.library`，是用来构建一个JNI类库的插件。
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
在model.android.ndk中定义ndk的属性:
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
再这里定义了类库的名字为:jni-lib,支持`armeabi`和`armeabi-v7a`平台，可以调用系统的log库。

##jni代码
JNI代码默认放在**module**的主目录下的`./src/main/jni`中，按照这个结构新建jni代码目录结构。
在jni的库中需要定义.h文件，这样可以直接`include`，定义llib.h文件
```
#include <android/log.h>

#define  LOG_TAG "jni-lib"
#define  LOGD(...) __android_log_print(ANDROID_LOG_DEBUG,LOG_TAG,__VA_ARGS__)

extern  void  callMethodFromJniLib();
```

llib.c中实现具体的方法：
```
#include "llib.h"

extern void callMethodFromJniLib() {
    LOGD("call methon from jni lib");
}
```

##引入jni-lib
在使用`app`项目调用jni-lib类库的时候，首先需要把`header`文件都给暴露出来，这样可以再其他的module中直接include，在modle.android.sources中指定需要暴露的文件:
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
通过`exportedHeaders`指定需要暴露出去的文件。

再其他module中引用的方式就比较简单了，直接指定jni的依赖即可,在`model.android.sources.main.jni`中指定依赖:
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
在`jni.dependencies`中指定`project`，linkage的默认值为`shared`。
这样就可以在app的jni中直接使用jni-lib中定义的方法了
```
#include <llib.h>


JNIEXPORT void JNICALL
Java_com_jjz_NativeUtil_callLogFromJni(JNIEnv *env, jclass type) {
    callMethodFromJniLib();//jni-lib中的方法
    __android_log_print(ANDROID_LOG_INFO, "jni-log", "from jni log");
    LOGW("log from  define");

}

```

源代码地址：https://github.com/jjz/android/tree/master/experimental

