#Apk包的破解与反编译
首先需要下载三个反编译的工具:
提取资源:`apktool`,http://ibotpeaches.github.io/Apktool/install/
反编译:`dex2jar`,https://github.com/pxb1988/dex2jar
查看源码工具:`jd-gui`,http://jd.benow.ca/
以开源中国的android版本为例，看下怎么进行破解和反编译apk。

##apktool
apk-tool是apk反编译的神器，用来提取apk内的资源。
下载apk-tool之后会得到一个jar包，运行:
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
提取资源成功：

这里有一个`smali`目录，它对应的是原始Android项目的src目录，就是Java的源代码。smali是Dalvik虚拟机指令语言，可以在https://github.com/JesusFreke/smali 这里查看smali的详细内容。

##dex2jar
`dex2jar`是一个能操作Android的delvik(.dex)文件格式和Java的（.class）的工具集合。主要功能有：
	1.d2j-dex2jar:执行dex到class的转换
	2.dex-reader-2.0/dex-writer-2.0:用于读写dex文件。
	3.smali/baksmali:smali工具
	4.其他
	
这里使用`dex2jar`主要是将apk反编译成java源码，方便阅读。
首先将apk文件改名为.zip文件。
解压zip包，可以看到一个`classes.dex`文件，这个就是java文件编译再通过dx工具打包而成的。
在`dex2jar`目录下:
>./d2j-dex2jar.sh ../oschina/osc-android-app-2.4/classes.dex

得到文件`classes-dex2jar.jar`,得到的这个文件可以使用`jd-gui`打开阅读。

##jd-gui
`jd-gui`是java的反编译器`Java Decompiler project`,把二进制的可执行文件翻译成代码。
`jd-gui`提供了一个图形化的界面:

可以通过`jd-gui`直接打开上面得到的文件`classes-dex2jar.jar`浏览代码:

	


