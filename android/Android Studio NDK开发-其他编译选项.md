#Android Studio NDK开发-其他编译选项
NDK即要支持交叉编译，还需要支持各个Android版本的编译，还有一些其他情况的编译，比如c和c++等。NDK提供了一些编译选项，用来支持不同的编译需求。
以前的NDK开发都是在`Android.mk`和`Appliction.mk`中设置,现在在`gradle-experimental`的`android.ndk{}`设置编译选项。
其中包含了设置:编译器配置，NDK版本,Header头文件位置,gcc编译器选项等...
下面介绍下几个常用的编译选项。
##platformVersion
大部分的App需要向下兼容，会设置`minSdkVersion`,从一个低版本的Sdk开始支持。同样的在低SDK版本上面运行的NDK也必须使用对应的NDK，就需要设置`platformVersion`。
设置`minSdkVersion`为14，对应的也需要设置`platformVersion`也是14:
```
model{
	android{
		ndk{
			....
			platfromVersion=14
		}
	}
}
```
也可以使用`paltformVersion "android-14"`设置NDK的版本。
##toolchain
`toolchain`是NDK下的一套文件系统和工具链，主要有GCC，编译adnroid的库和工具等，是本机编译工具或交叉编译工具。
在*gradle*中直接指定`toolchain`和`toolchainVersion`：
```
model {
    android {
       ......
       ndk {
       		......
            toolchain "clang"
            toolchainVersion "3.5"
           }
           ......
       }
     }    
```


##ABI
Android系统目前支持其中七种不同的CPU架构:ARMv5,ARMv7,x86,MIPS,ARMv8,MIP64,x86_64,每一种都关联着一个相应的ABI。
`Application Binary interface`应用程序二进制接口,定义了二进制文件（尤其是.so文件)是如何运行在相应的系统平台上的，从使用的指令集，内存到对齐到可用的系统函数库。在Android系统上，每一CPU架构对应一个ABI。
对应的.so文件会放在对应的ABI文件下面:


![ABI-文件夹](http://upload-images.jianshu.io/upload_images/22188-6befd1d9e77cee41.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


一般情况下，考虑到x86系统的市场份额还太小，还有为了减少apk包的大小，可以指定支持的平台，使用`abiFilters`,可以选择项目支持的平台,比如只支持`armabi`和`armeabi-v7a`的配置:
```
...

ndk {
	    ...
        abiFilters.addAll(['armabi', 'armeabi-v7a'])
        ...  
     }

 ...
```


##ldLibs
使用NDK开发Android应用时，会使用到系统自带的库，在之前的`*.mk`中的`LOCAL_LDLIBS`就是用来引用系统库的，现在使用在`Android studio`中可以配置`ldLibs`引用系统库。
例如在项目中链接android的`log`:
```
ndk {
       ...
        ldLibs.addAll(['log'])
   }
```

##CFlags和CppFlags
CFlag表示用于C编译器的选项。
CppFlags用来表示用于c++的编译器选项.
使用ldLibs告诉了链接器要链接那些库文件，而在CFlags和CppFlags可以用来指定.h文件的路径。
例如指定一个.h文件的目录:
```
 ndk {
       		...
            CFlags.addAll(['-I/usr/local/ssl/android-14/include'])
			...
        }
```
`CFlag`也是gcc的编译选项,比如使用`-Wall`表示打开警告开发，`-g`是要生成调试信用，生成的可执行文件具有和源代码关联的可调试信息。

##stl
stl是设置c++的运行时库的选项,默认使用的是`libstdc++`最小支持的c++库。
如果要使用`gnustl_static`（静态链接gnustl版本的stl）可以设置为:
```
 ndk {
            ...
            stl = 'gnustl_static'
            cppFlags.addAll(['-std=c++11'])
           	...
      }
```
在[这里](https://developer.android.com/ndk/guides/cpp-support.html#hr)可以看到NDK支持的对应的c++运行库,建议使用动态方法链接stl,这样可以减少apk的大小。




