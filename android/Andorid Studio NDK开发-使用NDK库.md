# Andorid Studio NDK开发-使用NDK库
`C语言`是一个巨大的宝库,Android是一个以Linux为基础的开源操作系统,系统底层很多的实现都是基于`C语言`开发，比如图像处理，加密等。另一方面`C语言`的运行效率也比Java开发要高很多，因此为了高效率的运行有时候也会使用`C语言`开发一些功能。再Android上面使用`C语言`开发就需要使用NDK，在使用NDK开发的过程中会使用大量的库，系统自带的库，第三方库以及自己写的库等。
使用`Android Studio`调用NDK的库是非常简便，NDK内置了一些库方便开发者使用比如：`Log库`，还有一些比较常用的第三方库比如:`OpenSSL`。下面会分别介绍下，这两种库的使用。

## 调用系统库
`Log`是在Android开发过程用来调试程序必备的工具之一，他会把日志信息输入到`Logcat`中，如何在NDK中使用`android.util.Log`方便在`Logcat`中查看JNI程序的运行情况呢？
这就需要在NDK开发中导入Android系统的`Log`库。首先需要在`gradle`中引入Log库，引入的方式使用是在`ldLibs`中添加：
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
直接gradle中的`ldLIbs`中加入log就可以了，如果还需要引入其他的系统库，只要在数组中直接增加即可。
下面来测试下`log库`的使用，先定义一个`native`的方法:
>public static native void callLogFromJni();

在JNI中调用Log库:
```
//引入 log
#include <android/log.h>

JNIEXPORT void JNICALL
Java_com_jjz_NativeUtil_callLogFromJni(JNIEnv *env, jclass type) {

    __android_log_print(ANDROID_LOG_INFO,"jni-log","from jni log");

}
```
第一个参数，`ANDROID_LOG_INFO`是Log的级别他包含：
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
一般我们常用的是
* `ADNROID_LOG_VERBOSE`->Log.v
* `ANDROID_LOG_DEBUG`->Log.d
* `ANDROID_LOG_INFO`-> Log.i
* `ANDROID_LOG_WARN`->Log.w
* `ANDROID_LOG_ERROR`->Log.e

第二个参数是tag，用来方便的对Log分类。
第三个参数是message，对应Log的具体信息。

一般会采用宏定义的方式，定义Log的输出的方法，方便调用,例如：
```
#define LOG_TAG "jni-log"
#define LOGW(...)  __android_log_print(ANDROID_LOG_WARN,LOG_TAG,__VA_ARGS__)

```
这里定义了一个`warning log`的宏，在代码里面可以直接调用:
```
  LOGW("log from  define");
```
系统库的调用比较简单方便，使用第三方库就比较麻烦些，第三方库需要使用NDK重新编译才能在JNI中调用。
## 调用第三方类库
`OpenSSL`是最常用的加密库，下面就以`OpenSSL`为例，介绍下在`gradle-experimental`中如何引入第三方类库。
关于如何编译`Android`下的`OpenSSL`详见：[Andorid Studio NDK 开发 - 编译 OpenSSL 类库](https://juejin.im/post/58dc5be1a0bb9f0069d17c1f)。
首先定义对于库的`repositories`,用来指定库的基本信息，包括库文件的路径，头文件的路径以及链接的方式等，详见如下代码:
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
c语言的类库分为`静态链接库(.a)`和`动态链接库(.so)`，静态类库和动态类库在引入方式上是不一样的，分为对应:
* `StaticLibraryBinary`->静态库
* `SharedLibraryBinary`-> 动态链接库

这里引入的库为静态链接库，库的repositories名称为:`openssl`.
定义好了一个`repositories`,现在就需要调用了，在`gradle`可以指定库的依赖：
```
model{
 ......
 android{
  .....
   sources {
            main {
                jni {
                    dependencies{
                        //静态链接库
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
              ....
            }
        }
 }
}
```
在`model.android.sources.main`中指定库的依赖,依赖的是上面定义的`openssl`,`linkage`类型为**static**,如果是动态链接库则`linkage`就是**shared**。
因为在编译`OpenSSL`设置了只支持`arm`结构的cpu，所以还需要指定`abi`为对应为arm架构，在`model.android`添加配置：
```
 ndk {
      moduleName "experiment"
      abiFilters.addAll(['armeabi', 'armeabi-v7a'])      
     }
```
定义了库的链接，就可以在代码中测试下`OpenSSL`的使用了。
首先定义一个`native`方法，该方法的目的是从`OpenSSL`中读取随机数：
```
public static native byte[] getRandom();
```

对应的`JNI`方法：
```
//引入OpenSSL的rand
#include <openssl/rand.h>
JNIEXPORT jbyteArray JNICALL
Java_com_jjz_NativeUtil_getRandom(JNIEnv *env, jclass type) {
    unsigned char rand_str[128];
    //使用OpenSSL的方法
    RAND_seed(rand_str, 32);
    jbyteArray bytes = (*env)->NewByteArray(env, 128);
    (*env)->SetByteArrayRegion(env, bytes, 0, 128, rand_str);
    return bytes;

}
```
`RAND_seed`是OpenSSL的方法，能够读取随机数。这段代码的意思就是读取一个`128位的随机数`，然后转换为Java的`byte数组`。
在界面上面使用实现出读取的随机数内容:
```
tv2.setText(Base64.encodeToString(NativeUtil.getRandom(), Base64.DEFAULT));
```
运行之后可以在界面看到一段随机的字符串显示：

![从openssl中读取随机数](http://upload-images.jianshu.io/upload_images/22188-555b303770d83872.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
可以看到使用`gradle-experiment`无论是调用系统库还是第三方库都比较简单。
以上源代码地址：[https://github.com/jjz/android/tree/master/UseLibrary](https://github.com/jjz/android/tree/master/UseLibrary)


