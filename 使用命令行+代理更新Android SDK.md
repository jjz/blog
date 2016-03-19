#使用命令行+代理更新Android SDK
在无桌面的Linux上面安装[Jenkins](https://segmentfault.com/a/1190000004639325)，要配置成Andorid 的持续集成环境[Jenkins持续集成Android项目](https://segmentfault.com/a/1190000004628020),需要在无桌面的Linux(ubuntu,centos)上安装Android SDK,
相对来说获取android-sdk-linux的安装包比较容易。
>wget http://dl.google.com/android/android-sdk_r23.0.2-linux.tgz  

这个包有时候在下不需要翻墙也可以成功下载，需要翻墙的话也可以在别的机器上面下载之后复制过来。
直接在系统上面解压就可以，如果需要更新`platform`和`platform-tool`就麻烦了。
这里会遇到两个问题：
	1.一般更新Andorid SDK都是使用SDK Manager直接更新
	2.更新Andorid，一个大家都知道的问题dl-ssl.google.com/android无法使用。

##Android SDK 命令
到Andorid的安装目录，运行命令:
>android -h

```
 Usage:
       android [global options] action [action options]
       Global options:
  -s --silent     : Silent mode, shows errors only.
  -v --verbose    : Verbose mode, shows errors, warnings and all messages.
     --clear-cache: Clear the SDK Manager repository manifest cache.
  -h --help       : Help on a specific command.

                                                                    Valid
                                                                    actions
                                                                    are
                                                                    composed
                                                                    of a verb
                                                                    and an
                                                                    optional
                                                                    direct
                                                                    object:
-    sdk              : Displays the SDK Manager window.
-    avd              : Displays the AVD Manager window.
-   list              : Lists existing targets or virtual devices.
-   list avd          : Lists existing Android Virtual Devices.
-   list target       : Lists existing targets.
-   list device       : Lists existing devices.
-   list sdk          : Lists remote SDK repository.
- create avd          : Creates a new Android Virtual Device.
-   move avd          : Moves or renames an Android Virtual Device.
- delete avd          : Deletes an Android Virtual Device.
- update avd          : Updates an Android Virtual Device to match the folders
                        of a new SDK.
- create project      : Creates a new Android project.
- update project      : Updates an Android project (must already have an
                        AndroidManifest.xml).
- create test-project : Creates a new Android project for a test package.
- update test-project : Updates the Android project for a test package (must
                        already have an AndroidManifest.xml).
- create lib-project  : Creates a new Android library project.
- update lib-project  : Updates an Android library project (must already have
                        an AndroidManifest.xml).
- create uitest-project: Creates a new UI test project.
- update adb          : Updates adb to support the USB devices declared in the
                        SDK add-ons.
- update sdk          : Updates the SDK by suggesting new platforms to install
                        if available.
```

可以看到这里有`update sdk`,`update adb`的升级命令。
更新Android SDK我们主要用到两个命令:
>android list sdk 
从服务器上获取到所有的Android的可用SDK.
>android update sdk
更新SDK,--filter参数可以用来选择要更新的SDK,一般情况下我们不会更新全部的SDK。也可以使用`android update sdk --no-ui --all --force`强制更新所有的SDK到电脑上。
##使用代理更新
运行命令`android list sdk `
一般情况下都会卡在:
```
Fetching https://dl.google.com/android/repository/addons_list-2.xml
```
因为大家都知道的原因google的服务无法使用。
还好android的命令提供了两个代理参数:`--proxy-host`设置代理的host地址,`--proxy-port`设置代理的端口。
国内的Android代理镜像服务器：
```
http://mirrors.neusoft.edu.cn 端口：80
http://ubuntu.buct.edu.cn/ 端口：80
http://ubuntu.buct.cn/ 端口：80
http://sdk.gdgshanghai.com 端口：8000
IPv6: http://ubuntu.buct6.edu.cn/ 端口：80
腾讯镜像:
http://android-mirror.bugly.qq.com 端口：8080

```
使用代理查看SDK列表：
>android list sdk --extended --proxy-host android-mirror.bugly.qq.com  --proxy-port 8080 -s
```
 id: 1 or "doc-23"
     Type: Doc
     Desc: Documentation for Android SDK, API 23, revision 1
----------
id: 2 or "android-21"
     Type: Platform
     Desc: Android SDK Platform 5.0.1
           Revision 2
.....
id: 20 or "sample-8"
     Type: Sample
     Desc: Android SDK Samples for Android API 8, revision 1
----------
.....
----------
id: 31 or "extra-android-gapid"
     Type: Extra
     Desc: GPU Debugging tools, revision 1.0.3
           By Android
           Tools that support GPU debugging and profiling within an IDE.
           Install path: extras/android/gapid
----------
id: 32 or "extra-android-m2repository"
     Type: Extra
     Desc: Local Maven repository for Support Libraries, revision 28
           By Android
           Install path: extras/android/m2repository
----------

  ......
```

使用代理更新指定SDK:
>android update sdk --no-ui --filter 2 --proxy-host android-mirror.bugly.qq.com  --proxy-port 8080 -s

安装` id: 2 or "android-21"`Android 21 的SDK。
安装的时候提示接受license:
```
Do you accept the license 'android-sdk-preview-license-52d11cd2' [y/n]:
```
选择y同意之后继续安装。
等待安装成功。
使用国内的镜像服务器安装，服务稳定，下载速度快，不需要翻墙。
注：其中id为32是extra-android-m2repository，android的`Support Libraries`。

