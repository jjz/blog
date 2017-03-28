#Android Studio NDK开发-JNI调用java函数
相对于NDK来说SDK里面有更多API可以调用，有的时候我们在做NDK开发的时候，需要在JNI直接调用Java中的函数，比如callback,系统信息等....
了解如何在JNI中调用Java方法，需要先了解`FindClass`和`GetMethodID`。
## FindClass和GetMethodID

在JNI中可以通过`FindClass`可以找到类，得到jclass，比如：
>jclass clz=(*env)->FindClass(env，"com/jjz/JniHandle");

`FindClass`的第二个参数需要传入类的路径。

使用GetMethodID获取类的方法,得到jmethodID,比如：
>jmethodID getStringFromJava=(*env)->GetMethodID(env,class,"getStringForJava","()V");

如果是静态函数需要使用`GetStaticMethodID`获取静态函数。
通过FindeClass可以找到JNI需要调用的类，Get*MethodID可以找到对应的方法，这样就可以在JNI中调用Java的方法了。
在Get*MethodID中，第四个参数是`()V`,这个是方法签名。

## 方法签名
在`Get*MethodID`的使用中，其中的第四个参数`()V`就是方法签名，因为java是支持重载的，所以需要标明函数的传参和返回值，就是方法的签名，用来保证方法的唯一。其中`()`代表不传参数，`V`代表返回值为void。
方法签名对于Java的参数都有一一对应的值。
方法签名中用大写的字母对应了java的基本数据类型:

* Z -> boolean
* B -> byte
* C -> char
* S -> short
* I -> int
* J -> long
* F -> float
* D -> double

其实就是有两个比较特殊的：boolean对应的是Z,long对应的J，其他的可以就是首个字母的大写。

数组的表示方法，以`[`为标志，一个`[`标识一维数组，[[表示二维数组

* byte[] -> [B
* int[][] -> [[I

引用类型的表示方法，需要以`L`开头，以`;`结束，中间对应类型的路径比如:

* String -> Ljava/lang/String;
* Object -> Ljava/lang/Object;

自定义的类的表示方法，比如包名为jjz.example，类名为JniHandle:

* jjz.example.JniHandle ->Ljjz/example/JniHandle;

除了手动输入类名和方法签名以外，JDK还提供了直接生成方法签名的工具`javap`。
到../app/build/intermediates/classes/debug下可以找到build之后生成的.class文件，运行命令:
>javap -s com/jjz/JniHandle

就可以得到类的所有的方法签名:

```
Compiled from "JniHandle.java"
public class com.jjz.JniHandle {
  public com.jjz.JniHandle();
    descriptor: ()V

  public static java.lang.String getStringFromStatic();
    descriptor: ()Ljava/lang/String;

  public java.lang.String getStringForJava();
    descriptor: ()Ljava/lang/String;
}
```

## 静态函数的调用
调用类的静态方法，首先要得到类的引用，再调用类的静态方法。
首先定义一个类和他的静态方法提供给JNI调用:

```

public class JniHandle {

    public static String getStringFromStatic() {
        return "string from static method in java";
    }
}
```
在JNI中调用Java的静态方法:

```

JNIEXPORT void JNICALL
Java_com_jjz_NativeUtil_callJavaStaticMethodFromJni(JNIEnv *env, jclass type) {

    jclass jniHandle = (*env)->FindClass(env, "com/jjz/JniHandle");
    if (NULL == jniHandle) {
        LOGW("can't find JniHandle");
        return;
    }
    jmethodID getStringFromStatic = (*env)->GetStaticMethodID(env, jniHandle, "getStringFromStatic",
                                                              "()Ljava/lang/String;");
    if (NULL == getStringFromStatic) {
        (*env)->DeleteLocalRef(env, jniHandle);
        LOGW("can't find method getStringFromStatic from JniHandle ");
        return;
    }
    jstring result = (*env)->CallStaticObjectMethod(env, jniHandle, getStringFromStatic);
    const char *resultChar = (*env)->GetStringUTFChars(env, result, NULL);
    (*env)->DeleteLocalRef(env, jniHandle);
    (*env)->DeleteLocalRef(env, result);
    LOGW(resultChar);

}
```
这里需要定义了一个`native`方法`com.jjz.NativeUtil.callJavaStaticMethodFromJni`,调用该方法可以在logcat中看到结果。

## 非静态函数的调用
调用非静态函数要复杂一些：

1. 通过findClass找到类
2. 通过GetMethodID得到构造函数
3. 通过调用构造函数得到一个类的实例
4. 通过GetMethodID得到需要调用的方法
5. 使用类的实例调用方法

首先定义类的方法：
```

public class JniHandle {
    public String getStringForJava() {
        return "string from method in java";
    }
}
```
再定义一个`native`方法，`com.jjz.NativeUtil.callJavaMethodFromJni`,在jni中实现该方法:
```

JNIEXPORT void JNICALL
Java_com_jjz_NativeUtil_callJavaMethodFromJni(JNIEnv *env, jclass type) {

    jclass jniHandle = (*env)->FindClass(env, "com/jjz/JniHandle");
    if (NULL == jniHandle) {
        LOGW("can't find jniHandle");
        return;
    }
    jmethodID constructor = (*env)->GetMethodID(env, jniHandle, "<init>", "()V");
    if (NULL == constructor) {
        LOGW("can't constructor JniHandle");
        return;
    }
    jobject jniHandleObject = (*env)->NewObject(env, jniHandle, constructor);
    if (NULL == jniHandleObject) {
        LOGW("can't new JniHandle");
        return;
    }
    jmethodID getStringForJava = (*env)->GetMethodID(env, jniHandle, "getStringForJava",
                                                     "()Ljava/lang/String;");
    if (NULL == getStringForJava) {
        LOGW("can't find method of getStringForJava");
        (*env)->DeleteLocalRef(env, jniHandle);
        (*env)->DeleteLocalRef(env, jniHandleObject);
        return;
    }
    jstring result = (*env)->CallObjectMethod(env, jniHandleObject, getStringForJava);
    const char *resultChar = (*env)->GetStringUTFChars(env, result, NULL);
    (*env)->DeleteLocalRef(env, jniHandle);
    (*env)->DeleteLocalRef(env, jniHandleObject);
    (*env)->DeleteLocalRef(env, result);
    LOGW(resultChar);

}
```
调用方法`com.jjz.NativeUtil.callJavaMethodFromJni`即可看到Java中的字符串传递给了JNI输出到了logcat上。

代码中的`DeleteLocalRef`的意思是释放局部引用，VM释放局部引用有两种方法

* 本地方法执行完毕之后VM自动释放
* 通过调用`DeleteLocalRef`手动释放

既然VM会自动释放为什么还要手动释放呢？
其实局部变量会阻止它所引用的对象被GC回收，它所引用的对象无法被GC回收，自己本身也就无法被自动释放，因此需要使用`DeleteLocalRef`。

源代码地址：https://github.com/jjz/android/tree/master/experimental


