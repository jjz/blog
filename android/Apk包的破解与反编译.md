# apk包的破解与反编译
处于学习和研究的目的，还有为了反破解和反逆向的目的，有时候需要破解apk，关于apk包的破解，需要使用三个反编译工具:
1.  提取资源:apktool, http://ibotpeaches.github.io/Apktool/install/
2. 反编译:dex2jar, https://github.com/pxb1988/dex2jar
3. 查看源码工具:jd-gui, http://jd.benow.ca/

这里以`开源中国`的android版本为例，看下利用上面的三个工具如何进行破解和反编译apk。

## apktool
`apk-tool`是apk反编译的神器，用来提取apk内的资源。
下载apk-tool之后会得到一个jar包，运行命令:
>java -jar apktool_2.1.0.jar decode oschina/osc-android-app-2.4.apk

```
I: Using Apktool 2.1.0 on osc-android-app-2.4.apk
I: Loading resource table...
I: Decoding AndroidManifest.xml with resources...
I: Loading resource table from file: /Users/jjz/Library/apktool/framework/1.apk
I: Regular manifest package...
I: Decoding file-resources...
I: Decoding values */* XMLs...
I: Baksmaling classes.dex...
I: Copying assets and libs...
I: Copying unknown files...
I: Copying original files...
```
提取资源成功会得到下图中的结果：

![提取资源](http://upload-images.jianshu.io/upload_images/22188-7e326a2af598d1ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
提取资源之后会有一个`smali`目录，它对应的是原始Android项目的`src目录`，也就是Java的源代码。*smali*是*Dalvik虚拟机指令语言*，可以在[https://github.com/JesusFreke/smali](https://github.com/JesusFreke/smali) 这里查看smali的更多内容。当然`smali`语言并不是我们的最终目的，`Java`源代码才是。

## dex2jar
`dex2jar`是一个能操作Android的*delvik(.dex)*文件格式和Java的（.class）的工具集合。主要功能有：

*  d2j-dex2jar:执行dex到class的转换
*  dex-reader-2.0/dex-writer-2.0:用于读写dex文件。
*  smali/baksmali:smali工具
	
使用`dex2jar`主要是将apk的`.dex`反编译成java源码，方便阅读。
首先将apk文件改名为`.zip文件`,然后解压`zip包`，可以得到一个`classes.dex`文件，这个就是java文件编译再通过`dx工具`打包而成的二进制文件。
运行`dex2jar`，反编译`classes.dex`:
>./d2j-dex2jar.sh ../oschina/osc-android-app-2.4/classes.dex

就可以得到文件`classes-dex2jar.jar`,这个`.jar`文件并没有直接阅读，但是可以使用`jd-gui`打开阅读代码。

## jd-gui
`jd-gui`是java的反编译器`Java Decompiler project`,把二进制的可执行文件翻译成代码。
`jd-gui`提供了一个图形化的界面:
![jd-gui](http://upload-images.jianshu.io/upload_images/22188-35417cc5cc03aaeb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以通过`jd-gui`直接打开上面得到的文件`classes-dex2jar.jar`，然后就可以浏览代码了:
![阅读代码](http://upload-images.jianshu.io/upload_images/22188-0d5a1b422e82f34d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

通过上面的步骤这样就得到apk的资源文件和源代码了，完成了apk的破解。

