#Andorid Studio NDK开发-LLDB调试
LLDB是一个高效的c/c++的调试器，他与LLVM编译器一起使用，提供了丰富的流程控制和数据检测,有效的帮忙我们调试程序。
LLDB是XCode中默认的调试器，Android Studio中可以在SDK Tools中下载LLDB最新版本，在Android Studio中和`gradle-experimental`一起调试NDK项目。

##LLDB安装
在Androis Studio中的Toolbar中可以找到Android的更新图标，打开可以看到Android SDK的升级配置，在SDK Tools中可以找到LLDB的安装选项。

![634A174A-C358-40DD-A54F-C51382FECDF0.png](http://upload-images.jianshu.io/upload_images/22188-e998f2f638af115e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


在上一篇的[Andorid Studio NDK开发-Experimental Plugin](http://www.jianshu.com/p/dc63d8997df2)中介绍了使用gradle-experimental可以简化NDK的开发配置，其中提到了在运行选项中有两个运行的配置选项`app`和`app-native`,其中的app-native就是用来运行和调试JNI开发的。

##Debug JNI
选择`app-native`，点击debug按钮可以直接进入Debug状态：

![debug](http://upload-images.jianshu.io/upload_images/22188-b7f5571df9640a3d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到程序在断点处暂停了，在左侧的状态里面可以看到变量的值和指针地址:

![variables](http://upload-images.jianshu.io/upload_images/22188-a15de7514a8e34b3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


##LLDB
从上面的图中可以看到除了`Variables`以外还有一个tab是`LLDB`,点击进入可以看到`(lldb)`的命令行，在这里面我们可以输入LLDB的命令，帮助调试程序。

![lldb](http://upload-images.jianshu.io/upload_images/22188-d989d52be1d54e6a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


##LLDB常用命令
* print (p)打印命令，打印变量以及其值
> p chars
```
(char [10]) $0 = "i am test"
```
* po 打印变量的值
>po chars
```
"i am test"
```
* expr 可以在调试时动态执行指定表达式，并打印结果，用于在调试过程中修改变量的值
比如我们在程序里面定义int b=1;可以在断点的时候使用expr
>expr b=10
```
(int) $0 = 10
``` 

* image用于寻址,可以用来栈地址对应的代码位置,image命令的参数比较多
 >image
 ```
 The following subcommands are supported:
      add          -- Add a new module to the current target's modules.
      dump         -- A set of commands for dumping information about one or
                      more target modules.
      list         -- List current executable and dependent shared library
                      images.
      load         -- Set the load addresses for one or more sections in a
                      target module.
      lookup       -- Look up information within executable and dependent
                      shared library images.
      search-paths -- A set of commands for operating on debugger target image
                      search paths.
      show-unwind  -- Show synthesized unwind instructions for a function.
For more help on any particular subcommand, type 'help <command> <subcommand>'.
 ```

* bt 命令用来堆栈信息

