自动化集成是当前的主流趋势，一般配置自动化集成环境都是采用`Server`版本，并没有桌面环境（没有UI），之前写了[CentOS 安装 Jenkins](http://www.jianshu.com/p/8a77010dafc6)，还有[Jenkins持续集成Android项目](https://segmentfault.com/a/1190000004628020),下面就需要在`Server`上安装`Android SDK`了,用来打包Android程序。
## 获取SDK安装包
获取`android-sdk-linux`的安装包，可以通过下面的命令直接获取。
>wget http://dl.google.com/android/android-sdk_r24.4.1-linux.tgz

这个包有时候在下不需要翻墙也可以成功下载，如果下载不下来就需要翻墙下载了，再上传到服务器上面，或者你的服务器可以直接翻墙。（推荐一个国内的镜像网站[http://tools.android-studio.org/index.php/sdk](http://tools.android-studio.org/index.php/sdk)也可以在这个网站上下载）。
直接解压安装包：
>tar zxvf  android-sdk_r24.4.1-linux.tgz

安装包解压之后，就需要下载`platform`，`platform-tool`以及`Android SDK`了。使用`Android Studio`是可以直接在`SDK Manager`下载的，但是`Server`上没有UI,所以需要使用命令行更新了。

## android 命令
到andorid的安装目录里的`tools`目录下，运行命令:
>android -h


     Usage:
       android [global options] action [action options]
       Global options:
      -s --silent     : Silent mode, shows errors only.
      -v --verbose    : Verbose mode, shows errors, warnings and all messages.
         --clear-cache: Clear the SDK Manager repository manifest cache.
      -h --help       : Help on a specific command.
    ...
    -    sdk              : Displays the SDK Manager window.
    -   list sdk          : Lists remote SDK repository.
    ...
    - update sdk          : Updates the SDK by suggesting new platforms to install if available.
    - update adb          : Updates adb to support the USB devices declared in the
                        SDK add-ons.


可以看到使用帮助中有`update sdk`,`update adb`的升级命令以及`list sdk`命令。
更新`Android SDK`主要用到两个命令,一个是从服务器上获取Android最新的可用SDK:
>android list sdk 

更新SDK的命令:
>android update sdk

一般情况下我们不需要更新全部的SDK，只需要选择性的更新部分SDK即可，可以使用`--filter`参数，来选择要更新的SDK，还可以使用`android update sdk --no-ui --all --force`强制更新所有的SDK到电脑上。
这里需要重点强调下`--all`参数，开发环境中不一定使用最新的SDK，有的时候就需要使用之前的`build tools`和`sdk`，这个时候可以使用参数`--filter`查到历史的SDK:
>android list sdk --all

列出所有的SDK,在选择更新sdk的时候使用`--filter`选择的时候也必须加上`--all`参数。
使用命令行更新比较麻烦的是遇到了个问题： 一般更新Andorid SDK都是使用`SDK Manager`直接更新,而因为墙的问题`dl-ssl.google.com/android`无法使用。
## 使用代理更新SDK
真实情况是运行命令`android list sdk `的时候，都会卡在:

    Fetching https://dl.google.com/android/repository/addons_list-2.xml

因为大家都知道的原因google的服务是无法使用的，还好`Android SDK Manager`命令提供了两个代理参数:
* `--proxy-host`设置代理的host地址
* `--proxy-port`设置代理的端口

可以通过代理使用国内的Android镜像，国内的Android代理镜像服务器：

    http://mirrors.neusoft.edu.cn 端口：80
    http://ubuntu.buct.edu.cn/ 端口：80
    http://ubuntu.buct.cn/ 端口：80
    http://sdk.gdgshanghai.com 端口：8000
    IPv6: http://ubuntu.buct6.edu.cn/ 端口：80

使用代理查看`SDK`列表：
>android list sdk  --proxy-host ubuntu.buct.edu.cn  --proxy-port 80 -s --all

     id: 1 or "doc-23"
     Type: Doc
     Desc: Documentation for Android SDK, API 23, revision 1
    ......

使用代理更新指定`SDK:`
>android update sdk --no-ui --filter 1 --proxy-host ubuntu.buct.edu.cn  --proxy-port 80 -s --all

这条命令会安装` id: 1 or "doc-23"`Android 23的文档 。
安装的时候会提示接受`license`:

    Do you accept the license 'android-sdk-preview-license-52d11cd2' [y/n]:

选择y同意之后才能继续安装，等待安装成功即可。
使用国内的镜像服务器安装，服务稳定，下载速度快，不需要翻墙。
* 注：其中extra-android-m2repository，就是android的`Support Libraries`。

## 可能会出现的错误
* 第一个可能会出现的错误
```
 Cannot run program "/var/lib/jenkins/tools/android-sdk/build-tools/23.0.1/aapt": error=2, No such file or directory
	at java.lang.ProcessBuilder.start(ProcessBuilder.java:1048)
	at com.android.builder.png.AaptProcess$Builder.start(AaptProcess.java:163)
	at com.android.builder.png.QueuedCruncher$1.creation(QueuedCruncher.java:106)
	at com.android.builder.tasks.WorkQueue.run(WorkQueue.java:203)
	at java.lang.Thread.run(Thread.java:745)
Caused by: java.io.IOException: error=2, No such file or directory
	at java.lang.UNIXProcess.forkAndExec(Native Method)
	at java.lang.UNIXProcess.<init>(UNIXProcess.java:248)
	at java.lang.ProcessImpl.start(ProcessImpl.java:134)
	at java.lang.ProcessBuilder.start(ProcessBuilder.java:1029)
	... 4 more
Thread(png-cruncher_2) has a null payload
```
因为使用aapt是32位的，不能在64位的系统上面运行，需要安装对32位的支持。
 CentOS安装命令:` sudo yum install libz.so.1`
Ubuntu安装命令:`sudo yum apt-get install lib32z1`

* 第二个可能会出现的错误
这个错误出现在`build tools`的版本为:25.0.0上面
>/build-tools/25.0.0/aapt:** /lib64/libc.so.6: version `GLIBC_2.14'     
not     found (required by /var/lib/jenkins/tools/android-sdk/build-tools/25.0.0/aapt)/var/lib/jenkins/tools/android-sdk/build-tools/25.0.0/aapt: /lib64/libc.so.6: version `GLIBC_2.14' not found (required by /var/lib/jenkins/tools/android-sdk/build-tools/25.0.0/lib64/libc++.so) FAILED

该问题出现的原因是因为`libc库`的版本低，CentOS 的libc版本为`2.14`，需要升级到`CentOS 7`才有支持`GLIBC_2.14`,如果不想升级CentOS,就需要自己编译`glibc 2.14`,编译的步骤:
>wget http://ftp.gnu.org/gnu/glibc/glibc-2.14.tar.gz
tar zxvf glibc-2.14.tar.gz
cd glibc-2.14
mkdir build
cd build
../configure -prefix=/opt/glibc-2.14
make -j4
sudo make install
export LD_LIBRARY_PATH=/opt/glibc-2.14/lib

这样就可以使用`glibc 2.14`了，但是aapt并没有指定使用编译的`glibc-2.14`，指定aapt使用`glibc-2.14`就可以解决上面的问题了。
首先将`build tools`中的aapt重命名为:`aapt_`
  >cd /path/to/build-tools
  mv aapt aapt_

再新建一个脚本`aapt`用来指定`glibc 2.14`的环境变量，设置环境变量之后再启动重命名的aapt_，脚本为:

    # !/bin/sh
    echo "$0"_$@
    export LD_LIBRARY_PATH=/opt/glibc-2.14/lib && "$0"_ $@

这样就解决了上面的错误，完成了`Android SDK`的安装

