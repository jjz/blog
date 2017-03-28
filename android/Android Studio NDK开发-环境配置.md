
## Android Studio NDK开发-环境配置
`NDK`全称**Native Development Kit**。NDK是一系列Android开发工具的集合，NDK提供了一系列的工具，可以帮助开发者在Android平台上面进行c/c++的开发，并能自动将.so打包金apk中。
NDK集成了交叉编译器，并提供`mk文件`可以做到隔离CPU,平台以及ABI等差异，只需修改`mk文件`即可。开发人员只需要简单修改mk文件，就可以创建出适应各平台的`.so`文件。NDK还提供了一份稳定的功能有限的`Android API`头文件声明。
NDK开发就是使用`c/c++`开发Android应用或者开发Android应用的某一个功能，使用NDK有时是为了调用底层的库,有时是为了`c/c++`开发带来的应用性能上的提高。
以前使用`Eclipse`做NDK开发是件很麻烦的事情，特别是配置文件的编写。在`Android Studio 1.3`之后，google和JetBrains合作，将CLion整合进了Android Studio,开发者可以免费使用CLion的功能对NDK进行开发，对于开发者来说是一个莫大的福利,现在使用`Android Studio` ，google在也提供了更多的支持，让NDK开发更加的容易。
## 环境要求
需要安装最新版的`Java`。之后要去先下载最新的Android Studio: [https://developer.android.com/studio/index.html ](https://developer.android.com/studio/index.html) 请自带梯子。也可以使用国内的镜像网站[http://www.android-studio.org/ ] (http://www.android-studio.org/) 下载。
Android Studio 会自动下载`Android SDK`,`Gradle`等开发工具。
而NDK就需要自己手动安装了。
## 安装NDK
在Toolbare可以找到`Android SDK`的更新图标:

![7D61BD4A-854A-49CF-BFB8-CAC0C552ABA4.png](http://upload-images.jianshu.io/upload_images/22188-429f1c728bf9e8a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在`SDK Tools`的Tab页中，勾选`LLDB 2.1`和`NDK`:

![8D94A004-08AA-45A1-892F-0435B88DB0B4.png](http://upload-images.jianshu.io/upload_images/22188-c35e90f554218376.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`LLDB`:是c/c++的调试器，可以用来做NDK开发的调试。


## 配置环境变量
`Android Studio`安装的NDK默认安装在`ANDROID_SDK_HOME/ndk-bundle/`下。需要设置NDK的环境变量（以fish）为例，打开配置文件：
>vim ~/.config/fish/config.fish

增加环境变量`NDK_HOME`:
>set -gx NDK_HOME $ANDROID_SDK_HOME/ndk-bundle

把`NDK_HOME`添加到`PATH`中：
>set -gx PATH  $ANDROID_SDK_HOME/platform-tools $ANDROID_SDK_HOME/tools $NDK_HOME  $PATH

让环境标量立即生效:
>source ~/.config/fish/config.fish

测试下ndk命令是否可以运行，运行命令:
>ndk-which gcc

得到正确结果:

    Users/..../Android/sdk/ndk-bundle/prebuilt/darwin-x86_64/bin/../../../toolchains/arm-linux-androideabi-4.9/prebuilt/darwin-x86_64/bin/arm-linux-androideabi-gcc

ndk-which命令运行正常,就意味着NDK环境变量设置正确。

注：`bash_profile`对应环境变量的设置:
>vim ~/.bash_profile
NDK_HOME=$ANDROID_SDK_HOME/ndk-bundle
PATH=$ANDROID_SDK_HOME/platform-tools:$ANDROID_SDK_HOME/tools:$NDK_HOME:$PATH
source ~/.bash_profile



