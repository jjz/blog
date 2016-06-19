#Andorid Studio NDK开发-使用库
在开发的过程中我们会调用大量的库，系统自带的库，第三方库等。在`gradle-experimental`中调用库更加的简单。

##调用系统库
Log是我们调试程序必备的工具之一，如何在NDK中使用`android.util.Log`方便在Logcat中查看JNI程序的运行情况。
首先需要在在`gradle`中引入log库：
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
在定义一个native的方法:
>public static native void callLogFromJni();

在Jni中调用log方法:
```
JNIEXPORT void JNICALL
Java_com_jjz_NativeUtil_callLogFromJni(JNIEnv *env, jclass type) {

    __android_log_print(ANDROID_LOG_INFO,"jni-log","from jni log");

}
```
第一个参数，`ANDROID_LOG_INFO`是log的级别他包含：
```
typedef enum android_LogPriority {
    ANDROID_LOG_UNKNOWN = 0,
    ANDROID_LOG_DEFAULT,    /* only for SetMinPriority() */
    ANDROID_LOG_VERBOSE,
    ANDROID_LOG_DEBUG,
    ANDROID_LOG_INFO,
    ANDROID_LOG_WARN,
    ANDROID_LOG_ERROR,
    ANDROID_LOG_FATAL,
    ANDROID_LOG_SILENT,     /* only for SetMinPriority(); must be last */
} android_LogPriority;

```
一般我们常用的是`ADNROID_LOG_VERBOSE`,`ANDROID_LOG_DEBUG`,`ANDROID_LOG_INFO`,`ANDROID_LOG_WARN`,`ANDROID_LOG_ERROR`分别对应java中的`Log.v`,`Log.d`,`Log.i`,`Log.w`,`Log.e`。
第二个参数是tag，用来方便的对log分类。
第三个参数是message，对应log的具体信息。

一般会采用宏定义的方式，定义LOG的输出：
```
#define LOG_TAG "jni-log"
#define LOGW(...)  __android_log_print(ANDROID_LOG_WARN,LOG_TAG,__VA_ARGS__)

```
定义了一个warning log的宏，在代码里面可以直接使用:
```
  LOGW("log from  define");
```

##使用第三方类库

c语言是一个巨大的宝库，底层的很多的实现都是基于C语言实现的，比如图像处理，加密等。为了避免重复发明轮子，会在NDK开发中引入第三库。
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
                // 静态类库的引用,
                binaries.withType(StaticLibraryBinary) {
                    staticLibraryFile = file("libs/libcrypto.a")
                }
                //动态类库的引用
//                binaries.withType(SharedLibraryBinary) {
//                    sharedLibraryFile = file("libs/libcrypto.so")
//                }
            }


        }

    }
  }
```
c语言的类库分为静态类库(.a)和动态类库(.so)，静态类库和动态类库的引入方式是不一样的，分为:`StaticLibraryBinary`和`SharedLibraryBinary`。这个引入的库为静态类库，名称为:`openssl`.

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
在model.android.sources.main中指定库的依赖为上面的openssl。`linkage`类型为static。
因为编译的OpenSSL只支持arm结构的cpu，因此需要指定abi为对应的cpu，在model.android添加配置å：
```
 ndk {
            moduleName "experiment"
            abiFilters.addAll(['armeabi', 'armeabi-v7a']) 
            
        }
```

##使用OpenSSL
定义一个`native`方法，从OpenSSL中读取随机数：
```
public static native byte[] getRandom();
```

生成对应的JNI方法：
```
//使用OpenSSL的rand
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
`RAND_seed`是OpenSSL的方法，读取随机数。

在界面上面使用读取OpenSSL随机数的方法:
```
tv2.setText(Base64.encodeToString(NativeUtil.getRandom(), Base64.DEFAULT));
```
运行之后可以在界面看到一段随机的字符串显示：


源代码地址：https://github.com/jjz/android/tree/master/experimental





