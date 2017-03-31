# Android Studio NDK开发-JNI调用java方法
相对于NDK来说SDK里面有更多API可以调用，有时候我们在做NDK开发的时候，需要在`JNI`直接Java中的方法和变量，比如`callback`,系统信息等....
如何在`JNI`中调用Java方法呢？就需要先了解`FindClass`和`GetMethodID`了。
## FindClass和GetMethodID

在JNI中可以通过`FindClass`可以找到Java类，得到`jclass`，例如：
>jclass clz=(*env)->FindClass(env，"com/jjz/JniHandle");

`FindClass`的第二个参数需要传入类的完整包名。

使用`GetMethodID`可以获取类的方法,得到jmethodID,例如：
>jmethodID getStringFromJava=(*env)->GetMethodID(env,class,"getStringForJava","()V");

如果调用的是静态方法需要使用`GetStaticMethodID`获取。通过`FindeClass`可以在JNI中找到需要调用的类，`GetMethodID`可以找到对应的方法，这样就可以在JNI中调用Java的方法了。
在GetMethodID中，第四个参数是`()V`,这个是方法签名。那么方法签名的规则又是怎么样呢？

## 方法签名
在`GetMethodID`中第四个参数`()V`就是方法签名，Java是支持重载的，所以需要标明方法的传参和返回值，这就是方法的签名。它是用来保证方法的唯一性。其中`()`代表不传参数，`V`代表返回值为**void**。
方法签名对于Java的类型都有一一对应的值。方法签名中用大写的字母对应了java的基本数据类型:

* Z -> boolean
* B -> byte
* C -> char
* S -> short
* I -> int
* J -> long
* F -> float
* D -> double

其实就是有两个比较特殊的：`boolean`对应的是Z,`long`对应的J，其他的都是首个字母的大写即可。

数组的表示方法，以`[`为标志，一个`[`标识一维数组，`[[`表示二维数组,例如:

* byte[] -> [B
* int[][] -> [[I

引用类型的表示方法，需要以`L`开头，以`;`结束，中间对应类型的包名加类名，例如:

* String -> Ljava/lang/String;
* Object -> Ljava/lang/Object;

自定义类的表示方法，比如包名为*jjz.example*，类名为*JniHandle*的表示方法:

* jjz.example.JniHandle ->Ljjz/example/JniHandle;

除了手动输入类名和方法签名以外，JDK还提供了直接生成方法签名的工具`javap`。
在`build`之后可以在路径`../app/build/intermediates/classes/debug`下可以找到build之后生成的`.class`文件，运行命令:
>javap -s com/jjz/JniHandle

就可以得到这个类的所有的方法签名:

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
有了类的引用，和方法的签名就可以直接在`JNI`中调用Java方法了，下面分别介绍下静态方法和类方法的调用。
## 静态方法的调用
调用类的静态方法，首先要得到类的引用，再调用类的静态方法。
先定义一个类和静态方法用来提供给`JNI`调用:
```
public class JniHandle {

    public static String getStringFromStatic() {
        return "string from static method in java";
    }
}
```
在定义了一个`native`方法`com.jjz.NativeUtil.callJavaStaticMethodFromJni`,生成这个方法的`JNI`代码,在`JNI`代码中调用*JniHandle*类的静态方法:
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
在Java中调用`com.jjz.NativeUtil.callJavaStaticMethodFromJni`可以该方法可以在logcat中看到`string from static method in java`,这样就完成了在`JNI`中调用了`Java`静态方法。

## 类方法的调用
调用类方法要更加的复杂一些，调用步骤：

1.  通过findClass找到类
2.  通过GetMethodID得到构造函数
3.  通过调用构造函数得到一个类的实例
4.  通过GetMethodID得到需要调用的方法
5.  使用类的实例调用方法

先定义一个类方法：
```
public class JniHandle {
    public String getStringForJava() {
        return "string from method in java";
    }
}
```
再定义一个`native`方法:`com.jjz.NativeUtil.callJavaMethodFromJni`,生成该方法的`JNI`代码，在`JMI`代码中实现调用`JniHandle`的类方法**getStringForJava**,代码如下:
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
调用方法`com.jjz.NativeUtil.callJavaMethodFromJni`即可看到Java中的字符串传递给了`JNI`最后输出到了**Logcat**上。

在上面的代码中有一个方法叫做`DeleteLocalRef`,它的意思是释放局部引用，`Android VM`释放局部引用有两种方法:

* 本地方法执行完毕之后`VM`自动释放
* 通过调用`DeleteLocalRef`手动释放

既然上面说了`VM`会自动释放引用为什么还需要手动释放呢？
其实某些局部变量会阻止它所引用的对象被GC回收，它所引用的对象无法被GC回收，自己本身也就无法被自动释放，因此需要使用`DeleteLocalRef`。而这里使用了JNI Local Reference，在JNI中引用了Java对象，如果不使用`DeleteLocalRef`释放的话，引用无法回收，就会造成内存泄露。

源代码地址：https://github.com/jjz/android/tree/master/experimental


