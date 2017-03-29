# Andorid Studio NDK开发-使用LLDB调试

`LLDB`是一个高效的`c/c++`的调试器，是与`LLVM编译器`一起使用，提供了丰富的流程控制和数据检测,有效的帮忙我们调试程序。`LLDB`也已经取代GDB成为XCode的默认调试器，`Android Studio`中也可以使用`LLDB`调试NDK程序，在`Android Studio`也中可以`LLDB`,从`SDK Tools`中下载`LLDB`最新版本，配合`Android Studio`和`gradle-experimental`一起调试NDK项目，会更加的方便。

## LLDB安装
在`Androis Studio`的`Toolbar`中可以找到Android的更新图标，打开可以看到Android SDK的升级配置，在`SDK Tools`中可以找到`LLDB`的安装选项。

![安装LLDB](http://upload-images.jianshu.io/upload_images/22188-e998f2f638af115e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

究竟如何使用`LLDB`调试NDK程序呢？在上一篇的[Andorid Studio NDK 开发 - NDK 开发利器 gradle-experimental](https://juejin.im/post/58d997dd44d90400694754d2 )中介绍了使用`gradle-experimental`可以简化NDK的开发配置，其中提到了在运行选项中有两个运行的配置选项`app`和`app-native`,其中的app-native就是用来运行和调试JNI开发的。`app-native`不仅仅可以直接运行，也可以进行Debug,选中`aap-native`之后，直接选择debug按钮就可以进入NDK的Debug模式

## Debug JNI

选择`app-native`，点击debug按钮可以直接进入Debug状态，在一段代码处设置一个断点，如图所示：

![断点](http://upload-images.jianshu.io/upload_images/22188-b7f5571df9640a3d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到程序运行到断点出，就进入了Debug状态，在左侧的状态里面可以看到变量的值和指针地址:

![变量信息](http://upload-images.jianshu.io/upload_images/22188-a15de7514a8e34b3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
程序进入了Debugz状态，但是并没有使用到`LLDB`,下面就使用下`LLDB`强大的功能。

## 使用LLDB

从上面的图中可以看到除了`Variables`的Tab页以外，还有一个Tab页就是`LLDB`,点击进入可以看到`(lldb)`的命令行，在命令行里面可以输入`LLDB`的命令，`LLDB`命令有很多强大的能力，比如，打印，寻址，调用堆栈等，通过这些命令可以有效的帮助调试NDK程序。

![lldb](http://upload-images.jianshu.io/upload_images/22188-d989d52be1d54e6a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
上图中使用p（print）打印命令打印出了`chars`变量的内容，打印的内容包括变量的类型:char[10]，变量的值："i am test"。

## LLDB常用命令

* print (p)打印命令，打印变量以及其值:
> p chars

    (char [10]) $0 = "i am test"

* po 仅打印变量的值:
>po chars

    "i am test"

* call 就是调用的意思，上述po和p也有调用的功能。一般只在不需要显示输出，或是方法无返回值时使用。例如定义一个变量int p=0,使用`call`命令:
>call p++

    (int) $0 = 1

* expr 可以在调试时动态执行指定表达式，并打印结果，用于在调试过程中修改变量的值
比如我们在程序里面定义int b=1;可以在断点的时候使用`expr`更改其值。
>expr b=10

    (int) $0 = 10 

 

* bt 命令用来堆栈信息,加all可打印所有thread的堆栈,比如我们增加一段导致崩溃的代码
>JNIEXPORT jstring JNICALL
Java_com_jjz_NativeUtil_firstNative(JNIEnv *env, jclass type) {
    char chars[] = "i am test";
    int p=0;
    p=1/0;
    return (*env)->NewStringUTF(env, chars);
}
运行的时候出现了崩溃这个时候可以运行`bt`命令,得到堆栈信息:
```
*thread #1, name = 'com.jjz', stop reason = signal SIGFPE
  * frame #0: 0xf6ff35e8 libc.so`tgkill + 12
    frame #1: 0xf6ff11ec libc.so`pthread_kill + 36
    frame #2: 0xf6fcd846 libc.so`raise + 14
    frame #3: libexperiment.so`__aeabi_idiv0 at lib1funcs.S:1337
    frame #4: libexperiment.so`Java_com_jjz_NativeUtil_firstNative(env=0xf4218ea0, type=0xff8b732c) at experiment.c:16
    frame #5: base.odex`java.lang.String com.jjz.NativeUtil.firstNative() at NativeUtil.java:0
    frame #6: base.odex`void com.jjz.MainActivity.onCreate(android.os.Bundle) at MainActivity.java:15
    frame #7: 0x729d58bf
```

* image用于寻址,可以用来查找栈地址对应的代码位置,image命令有很多的参数，可以帮助我们更加清晰的定位问题。
 >image lookup --address 0xfxxxxx
   有了对应的地址可以通过`image lookup`命令来查看该栈地址上面对应的代码。

