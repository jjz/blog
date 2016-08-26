#编译Android的OpenSSL类库
OpenSSL是一个强大的开源的安全套接字层密码库，包含了主要的密码学算法，常用的密钥和证书封装管理以及SSL协议。
Android上的开发对于安全的需求越来越高，虽然OpenSSL出现过几次漏洞，但OpenSSL仍然是在安全方便的使用最多的加密库。
OpenSSL是一个基于c开发的，古老的，开源的加密库，想在Android上使用OpenSSL必须要借助NDK,使用NDK编译成Android上面的动态连接库(或者静态链接库)，再借助JNI层的封装，提供给Java层调用。
这篇文章主要写的是如何构建Android的OpenSSL类库。参考OpenSSL的官方文档：https://wiki.openssl.org/index.php/Android

##前期准备
* OpenSSL的源代码
* setenv-adnroid.sh 构建脚本
* Make

OpenSSL已经托管在github上了,源代码地址：https://github.com/openssl/openssl
>git clone git@github.com:openssl/openssl.git

也可以在官网上下载最新的release版本：https://www.openssl.org/source/
由于OpenSSL项目的主干(master)上提交的是dev版本，最好把OpenSSL切换到最新的release版本上面
>git checkout OpenSSL_1_0_1t

setenv-android.sh脚本地址：https://wiki.openssl.org/images/7/70/Setenv-android.sh
>wget https://wiki.openssl.org/images/7/70/Setenv-android.sh

给脚本可以运行的权限
>chmod a+x Setenv-android.sh
安装`makedepend`
>brew install makedepend

##运行脚本
脚本下载完成之后是不能直接运行的，原因在于脚本里面的变量并没有配置，需要配置的变量:
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
电脑中已经设置了Android NDK和SDK变量的，只需要把正确的值配置到脚本就可以了,设置脚本中的变量:
```
ANDROID_NDK_ROOT=$NDK_HOME
_ANDROID_API="android-23"
_ANDROID_EABI="arm-linux-androideabi-4.9"
```
运行脚本:
>./Setenv-android.sh

没有`Error`出现就是配置正确了。

##编译OpenSSL

上面的配置会设置成环境变量
例如：
```
export MACHINE=armv7
export RELEASE=2.6.37
export SYSTEM=android
export ARCH=arm
```
根据上面的设置的环境变量，再运行`./config`就可以实现编译Android上library的配置，然后在make就可以了。
因为在Android设备上面运行，建议不要编译完整的OpenSSL库，官方给的建议选项：shared,no-ssl2,no-ssl3,no-comp,no-hw,no-engine.
OpenSSl的库需要安装到本地的电脑中，这样可以像使用NDK库一样的使用OpenSSL，编译命令可以设置`--openssldir`用来指定OpenSSL的安装目录。
```
cd openssl-1.0.1t
perl -pi -e 's/install: all install_docs install_sw/install: install_docs install_sw/g' Makefile.org
./config shared no-ssl2 no-ssl3 no-comp no-hw no-engine --openssldir=/usr/local/ssl/$ANDROID_API
```
运行到这里的时候得到一个这样的信息：
```
perating system: i686-apple-darwinDarwin Kernel Version 15.5.0: Tue Apr 19 18:36:36 PDT 2016; root:xnu-3248.50.21~8/RELEASE_X86_64
WARNING! If you wish to build 64-bit library, then you have to
         invoke './Configure darwin64-x86_64-cc' *manually*.
         You have about 5 seconds to press Ctrl-C to abort.
```
编译的OpenSSL是我的PC上的版本`darwin64-x86_64-cc`,并不是想要的Android版本。
查看下环境变量:
>echo $ANDROID_API

发现这个变量没有配置成功，上面的环境变量的配置（Setenv-android.sh）在我的电脑上面并没有起作用。
##优化脚本
根据OpenSSL在wiki中提到的脚本(Setenv-android.sh)，尝试下打印`$ANROID_API`的值。
在脚本里面更新打印内容：
>echo "ANDROID_API: `echo $ANDROID_API`"

得到结果：
```
...
ANDROID_API: android-23
...
```
可以看到变量的配置在`Setenv-android.sh`的生命周期内是有效的，因此我可以把编译的命令都放在`Setenv-adnroid.sh`中，在`Setenv-android.sh`的生命周期内运行完编译命令。
重新建一个脚本名字为`build-android-openssl.sh`
复制配置好的`Setenv-android.sh`的内容到`build-android-openssl.sh`,再添加命令:
```
cd openssl-1.0.1t
make clean
perl -pi -e 's/install: all install_docs install_sw/install: install_docs install_sw/g' Makefile.org
./config shared no-ssl2 no-ssl3 no-comp no-hw no-engine --openssldir=/usr/local/ssl/$ANDROID_API

make depend
make all
sudo -E make install CC=$ANDROID_TOOLCHAIN/arm-linux-androideabi-gcc RANLIB=$ANDROID_TOOLCHAIN/arm-linux-androideabi-ranlib

```
可以看到`/usr/local/ssl/android-23`有生成了对应的库和文件。
OpenSSL源代码目录下生成了：`libcrypto.so`和`libcrypto.a`
Android的OpenSSL库编译完成，下一步就可以尝试在NDK中引用OpenSSL了。

优化后的脚本地址：https://github.com/jjz/script/blob/master/build-android-openssl.sh

