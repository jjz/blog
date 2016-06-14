#Android Studio NDK开发-环境配置
NDK全称是 Native Development Kit。NDK是一系列工具的集合，NDK提供了一系列的工具，帮助开发者进行c/c++的开发，并能自动将.so打包成apk。NDK集成了交叉编译器，并提供了相应的mk文件隔离CPU,平台，ABI等差异。开发人员只需要简单修改mk文件，就可以创建出.so。NDK还提供了一份稳定的功能有限的API头文件声明。
NDK开发就是用c/c++开发Android应用，有时是为了调用底层的库，还有就是c/c++的效率更加的高。以前使用Eclipse做NDK开发各种麻烦的事情，配置文件的编写等各种的麻烦。在Android Studio 1.3之后，goole和JetBrains合作，将CLion整合进了Android Studio,并且免费支持NDK开发。现在使用Android Studio ，google提供了更多的支持，让NDK开发更加的容易。

安装最新版的Java
然后要先下载Android Studio: https://developer.android.com/studio/index.html,请自带梯子。

##安装NDK
在Toolbare可以找到Android的更加图标:

在`SDK Tools`的Tab页中，勾选LLDB 2.1和NDK:

LLDB是c/c++的调试器，用来做NDK开发的调试。

##配置环境变量
Android Studio安装的NDK默认安装在ANDROID_SDK_HOME/ndk-bundle/下。需要设置NDK的环境变量（以fish）为例：
>vim ~/.config/fish/config.fish
增加变量NDK_HOME:
>set -gx NDK_HOME $ANDROID_SDK_HOME/ndk-bundle
设置PATH：
>set -gx PATH  $ANDROID_SDK_HOME/platform-tools $ANDROID_SDK_HOME/tools $NDK_HOME  $PATH
让变更立刻生效:
>source ~/.config/fish/config.fish
测试下ndk命令是否可以运行，运行:
>ndk-which gcc
得到结果:
```
Users/..../Android/sdk/ndk-bundle/prebuilt/darwin-x86_64/bin/../../../toolchains/arm-linux-androideabi-4.9/prebuilt/darwin-x86_64/bin/arm-linux-androideabi-gcc
```
ndk-which命令运行正常。
bash_profile对应的设置为:
```
vim ~/.bash_profile
NDK_HOME=$ANDROID_SDK_HOME/ndk-bundle
PATH=$ANDROID_SDK_HOME/platform-tools;$ANDROID_SDK_HOME/tools;$NDK_HOME;$PATH
source ~/.bash_profile
```







