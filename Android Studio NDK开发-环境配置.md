#Android Studio NDK开发-环境配置
`NDK`全称是 **Native Development Kit**。NDK是一系列工具的集合，NDK提供了一系列的工具，可以帮助开发者进行c/c++的开发，并能自动将.so打包成apk。NDK集成了交叉编译器，并提供了相应的mk文件可以做到隔离CPU,平台，ABI等差异，只需修改mk文件即可。开发人员只需要简单修改mk文件，就可以创建出.so。NDK还提供了一份稳定的功能有限的API头文件声明。
NDK开发就是用c/c++开发Android应用或者开发应用的某一个功能，使用NDC有时是为了调用底层的库,有时是为c/c++带来的效率的提高。
以前使用Eclipse做NDK开发各种麻烦的事情，配置文件的编写等各种的麻烦。在Android Studio 1.3之后，google和JetBrains合作，将CLion整合进了Android Studio,免费支持对于NDK开发，对于开发者来说是一个莫大的福利,现在使用Android Studio ，google在提供了更多的支持，让NDK开发更加的容易。
环境要求
安装最新版的`Java`
然后要先下载Android Studio: https://developer.android.com/studio/index.html 请自带梯子。
Android Studio 会自动下载Android SDK,Gradle,已经对于的开发工具。
NDK需要自己手动安装
##安装NDK
在Toolbare可以找到Android的更新图标:

![7D61BD4A-854A-49CF-BFB8-CAC0C552ABA4.png](http://upload-images.jianshu.io/upload_images/22188-429f1c728bf9e8a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在`SDK Tools`的Tab页中，勾选LLDB 2.1和NDK:

![8D94A004-08AA-45A1-892F-0435B88DB0B4.png](http://upload-images.jianshu.io/upload_images/22188-c35e90f554218376.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

LLDB是c/c++的调试器，用来做NDK开发的调试。

##配置环境变量
Android Studio安装的NDK默认安装在`ANDROID_SDK_HOME/ndk-bundle/`下。需要设置NDK的环境变量（以fish）为例：
>vim ~/.config/fish/config.fish

增加变量`NDK_HOME`:
>set -gx NDK_HOME $ANDROID_SDK_HOME/ndk-bundle

设置`PATH`：
>set -gx PATH  $ANDROID_SDK_HOME/platform-tools $ANDROID_SDK_HOME/tools $NDK_HOME  $PATH

让变更立刻生效:
>source ~/.config/fish/config.fish

测试下ndk命令是否可以运行，运行:
>ndk-which gcc

得到结果:
```
Users/..../Android/sdk/ndk-bundle/prebuilt/darwin-x86_64/bin/../../../toolchains/arm-linux-androideabi-4.9/prebuilt/darwin-x86_64/bin/arm-linux-androideabi-gcc
```
ndk-which命令运行正常,NDK环境变量设置完成。
bash_profile对应的设置为:
```
vim ~/.bash_profile
NDK_HOME=$ANDROID_SDK_HOME/ndk-bundle
PATH=$ANDROID_SDK_HOME/platform-tools:$ANDROID_SDK_HOME/tools:$NDK_HOME:$PATH
source ~/.bash_profile
```






