#Android Studio NDK开发-其他编译选项
NDK的编译不仅仅要支持交叉编译，还需要支持各个版本的编译。NDK提供了一些编译选项，用来支持不同的编译需求。
编译选项在`android.ndk{}`中设置，编译选项的设置一般都是再`Android.mk`和`Appliction.mk`中设置。其中包含了，编译器配置，NDK版本，Header头文件位置等...
下面简单介绍下几个常用的编译选项。
> ##platformVersion
大部分的App需要向下兼容，在低SDK版本上面运行的NDK也必须使用对应的低版本的NDK，这时就需要设置`platformVersion`。
设置`minSdkVersion`为14，对应的需要设置`platformVersion`也为14:
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
>##toolchain
`toolchain`是NDK下的一套文件系统和工具链，主要包括GCC，可以当做本机编译工具或交叉编译工具。可以在*gradle*中直接指定`toolchain`和`toolchainVersion`：
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


>##ABI
Android系统目前支持其中不同的CPU架构:ARMv5,ARMv7,x86,MIPS,ARMv8,MIP64,x86_64,每一种都关联着一个相应的ABI。
`Application Binary interface`应用程序二进制接口,定义了二进制文件（尤其是.so文件)是如何运行在相应的系统平台上的，从使用的指令集，内存到对齐到可用的系统函数库。在Android系统上，每一CPU架构对应一个ABI。
对应的.so文件会放在对应的ABI文件下面:

一般情况下，考虑到x86系统的市场份额还太小，还有为了减少apk包的大小，需要指定支持的平台，就需要使用`abiFilters`，比如只需要支持`armabi`和`armeabi-v7a`:
```
...
ndk {
			...
        abiFilters.addAll(['armabi', 'armeabi-v7a'])
        ...  
     }
 ...
```


>##ldLibs
在使用NDK开发Android应用时，会使用系统自带的库，之前的`.mk`中的`LOCAL_LDLIBS`就是用来引用系统库的，现在使用ndk的`ldLibs`引用系统库，例如在项目中链接android的`log`:
```
ndk {
       ...
        ldLibs.addAll(['log'])
   }
```

>##CFlags和CppFlags
CFlag表示用于C编译器的选项。
CppFlags用来表示用于c++的编译器选项.
使用ldLibs告诉了链接器要链接那些库文件，在CFlags和CppFlags可以用来指定.h文件的路径。
例如指定.h文件的目录:
```
 ndk {
       		...
            CFlags.addAll(['-I/usr/local/ssl/android-14/include'])
				...
        }
```
CFlag 也是gcc的编译选项,比如使用`-Wall`表示打开警告开发，`-g`是要生成调试信用，生成的可执行文件具有和源代码关联的可调试信息。

>##stl
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
在这里可以看到NDK支持的对应的c++运行库：[https://developer.android.com/ndk/guides/cpp-support.html#hr](https://developer.android.com/ndk/guides/cpp-support.html#hr)建议使用动态方法链接stl,这样可以减少apk的大小




