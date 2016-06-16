#Andorid Studio NDK开发-LLDB调试
LLDB是一个高效的c/c++的调试器，他与LLVM编译器一起使用，提供了丰富的流程控制和数据检测。LLDB也XCode中默认的调试器，Android Studio中可以在SDK Tools中下载LLDB最新版本，可以在Android Studio中和gradle-experimental一起调试NDK项目。

LLDB安装,Toolbar中的android更新图标中，打开可以看到Android SDK的升级配置，在SDK Tools中可以找到LLDB的安装选项。

在上一篇的[Andorid Studio NDK开发-Experimental Plugin](http://www.jianshu.com/p/dc63d8997df2)中介绍了使用gradle-experimental可以简化NDK的开发配置，其中提到了在运行选项中有两个运行的配置选项`app`和`app-native`,其中的app-native就是用来运行和调试JNI开发的。

##Debug 
选择`app-native`，点击debug按钮可以直接进入Debug状态：

可以看到程序在断点暂停了，在左侧的状态里面可以看到变量的值和指针地址等：

##LLDB
从上面的图中可以看到除了`Variables`以外还有一个tab是`LLDB`,点击进入可以看到`(lldb)`的命令行，在这里面我们可以输入LLDB的命令，帮助调试程序。

##LLDB常用命令
* print (p)打印命令，可以打印变量相关信息
>p chars
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

      add          -- Add a new module to the current target's modules.
      dump         -- A set of commands for dumping information about one or
                      more target modules.
      list         -- List current executable and dependent shared library
                      images.
      load         -- Set the load addresses for one or more sections in a
                      target module.
      lookup       -- Look up information within executable and dependent
                      shared library images.
      search-paths -- A set of commands for operating on debugger target image
                      search paths.
      show-unwind  -- Show synthesized unwind instructions for a function.

For more help on any particular subcommand, type 'help <command> <subcommand>'.
 ```

* bt 命令用来堆栈信息


