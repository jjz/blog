# 编译Android的OpenSSL类库

`OpenSSL`是一个强大的开源安全套接字层密码库，它包含了主要的密码学算法，常用的密钥和证书封装管理以及SSL协议,并提供丰富的应用程序供测试或其他目的使用。
在`Android`上开发对于安全的需求越来越高，虽然`OpenSSL`出现过几次漏洞，但它仍然是在安全方面的使用最多的加密库之一。
`OpenSSL`是一个基于c语言开发的，古老的，开源的加密库，想要在`Android`上使用`OpenSSL`必须要借助`NDK`,先使用NDK编译成Android上面的动态连接库(或者静态链接库)，再借助JNI层的封装，提供给Java层调用。
这篇文章主要写的是如何编译`Android`的`OpenSSL`类库。参考OpenSSL的官方文档：[https://wiki.openssl.org/index.php/Android](https://wiki.openssl.org/index.php/Android)

## 前期准备

环境准备:
* 编译环境为MacOS
* OpenSSL的源代码
* Setenv-android.sh 构建脚本
* 安装Make
* 安装makedepend


`OpenSSL`可以在github上找到源代码,源代码地址：[https://github.com/openssl/openssl](https://github.com/openssl/openssl)
>git clone git@github.com:openssl/openssl.git

也可以在官网上下载最新的release版本：https://www.openssl.org/source/
由于`OpenSSL`项目的主干(master)上提交的是开发分支，最好把`OpenSSL`切换到最新的release版本上面
>git checkout OpenSSL_1_1_0e

`Setenv-android.sh`是用来编译Android上的OpenSSL的脚本，下载地址：[https://wiki.openssl.org/images/7/70/Setenv-android.sh](https://wiki.openssl.org/images/7/70/Setenv-android.sh)
>wget https://wiki.openssl.org/images/7/70/Setenv-android.sh

给`Setenv-android.sh`脚本可以运行的权限
>chmod a+x Setenv-android.sh

安装`makedepend`
>brew install makedepend

## 运行脚本
`Setenv-android.sh`脚本的作用是用来给编译OpenSSL配置Android编译的环境变量的。脚本下载完成之后是不能直接运行，原因在于脚本里面的变量并没有配置，需要配置变量:
```
ANDROID_NDK_ROOT:
ANDROID_ARCH: arch-arm
ANDROID_EABI: arm-linux-androideabi-4.9
ANDROID_API: android-23
ANDROID_SYSROOT: /platforms/android-23/arch-arm
ANDROID_TOOLCHAIN:
FIPS_SIG:
CROSS_COMPILE: arm-linux-androideabi-
ANDROID_DEV: /platforms/android-23/arch-arm/usr
```
已经设置了`Android NDK`和`Android SDK`变量的，只需要把正确的值配置到脚本就可以了,设置脚本中的变量:
```
ANDROID_NDK_ROOT=$NDK_HOME
_ANDROID_API="android-23"
_ANDROID_EABI="arm-linux-androideabi-4.9"
```
运行脚本:
>./Setenv-android.sh

没有`Error`出现就表示配置正确了。


## 编译OpenSSL

上面的配置已经给OpenSSl的编译环境设置了环境变量例如：
```
export MACHINE=armv7
export RELEASE=2.6.37
export SYSTEM=android
export ARCH=arm
```
根据上面的设置的环境变量，再运行`./config`就可以实现编译Android上OpenSSL的配置,make就可以开始编译了。
因为在Android设备上面运行，建议不要编译完整的OpenSSL库，官方给的建议编译Android的选项：
>shared,no-ssl2,no-ssl3,no-comp,no-hw,no-engine

编辑`OpenSSl`的类库可以安装到本地，这样可以像使用NDK中其他库一样的使用OpenSSL，编译命令可以设置`--openssldir`用来指定OpenSSL的安装目录。

>cd openssl-1.0.1t
perl -pi -e 's/install: all install_docs install_sw/install: install_docs install_sw/g' Makefile.org
./config shared no-ssl2 no-ssl3 no-comp no-hw no-engine --openssldir=/usr/local/ssl/$ANDROID_API

运行到这里的时候得到一个这样的信息：
```
perating system: i686-apple-darwinDarwin Kernel Version 15.5.0: Tue Apr 19 18:36:36 PDT 2016; root:xnu-3248.50.21~8/RELEASE_X86_64
WARNING! If you wish to build 64-bit library, then you have to
         invoke './Configure darwin64-x86_64-cc' *manually*.
         You have about 5 seconds to press Ctrl-C to abort.
```
编译的`OpenSSL`使用的是本地的`darwin64-x86_64-cc`,并不是想要的`arm-linux-androideabi-gcc`编译的，编译的这个类库是不能在Android上面使用的。原因是什么呢？查看了下环境变量:
>echo $ANDROID_API

发现这个变量没有配置，上面的环境变量的配置`Setenv-android.sh`在设置的环境变量并没有起作用。

## 优化编译脚本
在`Setenv-android.sh`中，尝试下打印`$ANROID_API`的值，打印`$ANDROID_API`的内容：
>echo "ANDROID_API: `echo $ANDROID_API`"

得到结果为：
```
...
ANDROID_API: android-23
...
```
可以看到`$ANDROID_API`变量在`Setenv-android.sh`的生命周期内是有效的，而脚本运行结束之后设置的变量没有设置成功这个应该是因为我使用了`fish`有关,因此我就考虑把编译的命令都放在`Setenv-adnroid.sh`里，在`Setenv-android.sh`的生命周期内运行完所有的编译命令。
重新建一个脚本名字为`build-android-openssl.sh`
复制配置好的`Setenv-android.sh`的内容到`build-android-openssl.sh`,再添加命令:
```
cd openssl
make clean
perl -pi -e 's/install: all install_docs install_sw/install: install_docs install_sw/g' Makefile.org
./config shared no-ssl2 no-ssl3 no-comp no-hw no-engine --openssldir=/usr/local/ssl/$ANDROID_API

make depend
make all
sudo -E make install CC=$ANDROID_TOOLCHAIN/arm-linux-androideabi-gcc RANLIB=$ANDROID_TOOLCHAIN/arm-linux-androideabi-ranlib
```
注：该脚本和`openssl`源码的目录是同一级目录。
完成编译之后可以看到`/usr/local/ssl/android-23`有生成了对应的库和文件,OpenSSL源代码目录下生成了：`libcrypto.so`和`libcrypto.a`
这样就完成了Android的`OpenSSL`库编译完成，下一步就可以尝试在NDK中引用OpenSSL了。

优化后的脚本地址：[https://github.com/jjz/script/blob/master/build_android_openssl.sh](https://github.com/jjz/script/blob/master/build_android_openssl.sh)

