#Andorid Studio NDK开发-使用库
C语言是一个巨大的宝库，系统底层的很多的实现都是基于C语言实现的，比如图像处理，加密等。
C语言的运行效率也是很高的，因此为了效率有时候也会引入第三方的C语言库。
总而言之，会在NDK开发的过程中会使用大量的库，系统自带的库，第三方库等。
在`gradle-experimental`中使用C语言的库是非常便利的。

## 使用系统库
Log是在Android开发过程用来调试程序必备的工具之一，如何在NDK中使用`android.util.Log`方便在Logcat中查看JNI程序的运行情况呢？这就需要在NDK中导入Android系统的`Log`库。
首先需要在在`gradle`中引入Log库：
```
model{
 ....
 android {
        compileSdkVersion 23
        buildToolsVersion "23.0.2"
        ndk {
            moduleName "experiment"    
            ldLibs.addAll([ 'log']);
        }
   }
 }
```
直接在ldLIbs中加入log就可以，如果需要引入其他的系统库，只要在数组中直接增加即可。
再定义一个native的方法:
>public static native void callLogFromJni();

在Jni中调用Log库的方法:
```
//引入 log
#include <android/log.h>

JNIEXPORT void JNICALL
Java_com_jjz_NativeUtil_callLogFromJni(JNIEnv *env, jclass type) {

    __android_log_print(ANDROID_LOG_INFO,"jni-log","from jni log");

}
```
第一个参数，`ANDROID_LOG_INFO`是log的级别他包含：
```
typedef enum android_LogPriority {
    ANDROID_LOG_UNKNOWN = 0,
    ANDROID_LOG_DEFAULT,    /* only for SetMinPriority() */
    ANDROID_LOG_VERBOSE,
    ANDROID_LOG_DEBUG,
    ANDROID_LOG_INFO,
    ANDROID_LOG_WARN,
    ANDROID_LOG_ERROR,
    ANDROID_LOG_FATAL,
    ANDROID_LOG_SILENT,     /* only for SetMinPriority(); must be last */
} android_LogPriority;

```
一般我们常用的是`ADNROID_LOG_VERBOSE`,`ANDROID_LOG_DEBUG`,`ANDROID_LOG_INFO`,`ANDROID_LOG_WARN`,`ANDROID_LOG_ERROR`分别对应java中的`Log.v`,`Log.d`,`Log.i`,`Log.w`,`Log.e`。
第二个参数是tag，用来方便的对log分类。
第三个参数是message，对应log的具体信息。

一般还会采用宏定义的方式，定义Log的输出的方法，方便调用：
```
#define LOG_TAG "jni-log"
#define LOGW(...)  __android_log_print(ANDROID_LOG_WARN,LOG_TAG,__VA_ARGS__)

```
这里定义了一个warning log的宏，在代码里面可以直接调用:
```
  LOGW("log from  define");
```

## 使用第三方类库
OpenSSL是最常用的加密库之一，下面以OpenSSL为例，介绍下在`gradle-experimental`中如何引入第三方类库。关于如何编译Android下的OpenSSL详见：[编译Android的OpenSSL类库](http://www.jianshu.com/p/1b60337991f9)。
首先定义对于库的`repositories`:
```
model {
   repositories{
        libs(PrebuiltLibraries) {
            // Configure one pre-built lib: static
            openssl {
                // 头文件地址
                headers.srcDir "/usr/local/ssl/android-23/include"
                // 静态链接库的引用,
                binaries.withType(StaticLibraryBinary) {
                    staticLibraryFile = file("libs/libcrypto.a")
                }
                //动态链接库的引用
//                binaries.withType(SharedLibraryBinary) {
//                    sharedLibraryFile = file("libs/libcrypto.so")
//                }
            }

        }

    }
  }
```
c语言的类库分为静态链接库(.a)和动态链接库(.so)，静态类库和动态类库的引入方式是不一样的，分为对应:`StaticLibraryBinary`和`SharedLibraryBinary`。这里引入的库为静态链接库，名称为:`openssl`.

指定库依赖：
```
model{
 ......
 android{
  .....
   sources {
            main {
                jni {
                    dependencies{
                        library 'openssl' linkage 'static'
                        //动态链接库
                        //  library 'openssl' linkage 'shared'
                    }
                    source {
                        srcDir "src/main/jni"
                    }
                }
                jniLibs{
                    source{
                        srcDir "libs/"
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
这里在model.android.sources.main中指定库的依赖为上面定义的`openssl`,`linkage`类型为static,如果是动态链接库`linkage`就是shared。
因为编译的OpenSSL只支持arm结构的cpu，因此需要指定abi为对应的cpu，在model.android添加配置：
```
 ndk {
      moduleName "experiment"
      abiFilters.addAll(['armeabi', 'armeabi-v7a'])      
     }
```

## 使用OpenSSL
首先定义一个`native`方法，需要从OpenSSL中读取随机数：
```
public static native byte[] getRandom();
```

生成对应的JNI方法：
```
//引入OpenSSL的rand
#include <openssl/rand.h>
JNIEXPORT jbyteArray JNICALL
Java_com_jjz_NativeUtil_getRandom(JNIEnv *env, jclass type) {
    unsigned char rand_str[128];
    RAND_seed(rand_str, 32);
    jbyteArray bytes = (*env)->NewByteArray(env, 128);
    (*env)->SetByteArrayRegion(env, bytes, 0, 128, rand_str);
    return bytes;

}
```
`RAND_seed`是OpenSSL的方法，读取随机数。这段代码就是读取一个128的随机数，然后转换为java的byte[]。

在界面上面使用读取随机数的方法:
```
tv2.setText(Base64.encodeToString(NativeUtil.getRandom(), Base64.DEFAULT));
```
运行之后可以在界面看到一段随机的字符串显示：

![从openssl中读取随机数](http://upload-images.jianshu.io/upload_images/22188-555b303770d83872.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

源代码地址：https://github.com/jjz/android/tree/master/experimental



