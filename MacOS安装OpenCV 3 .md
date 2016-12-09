OpenCV（Open Source Computer Vision Library）是一个基于开源发行的跨平台计算机视觉库，而OpenCV 3的2014发布的OpenCV 2的升级版本，它带来的改变:

* 大体上保留了OpenCV 2的经典c++和python编程接口的风格
* 架构调整
* 更多新的算法
* 引入T-API,使OpenCL加速更容易
* 更多指令优化集
在MacOS Sierra上安装OpenCV 3是件很折腾的事情。
##准备
需要先安装cmake:
>brew install cmake
在OpenCV的官网上面下载代码，下载地址:[http://opencv.org/](http://opencv.org/),叶可以在github上面下载源代码:[https://github.com/opencv/opencv](https://github.com/opencv/opencv),是github下载的源代码需要切换到3.1.0的tag：
>git tag 3.1.0
当然我们也可以选择使用Homebrew安装，这样比较便捷，这里以使用Homebrew安装为例。
##安装
安装命令是:
>brew opencv3
这时候遇到了第一个问题:
```
 XXX/cap_qtkit.mm:46:9: fatal error:
   'QTKit/QTKit.h' file not found
   #import 
    ^
   1 error generated.
   make[2]: *** [modulesideoio/CMakeFiles/opencv_videoio.dir/src/cap_qtkit.mm.o] Error 1
   make[1]: *** [modulesideoio/CMakeFiles/opencv_videoio.dir/all] Error 2
   make: *** [all] Error 2
```
原因是MacOS Sierra中将原有的QTKit.framework进行了更改，移除了部分文件，将QTKit升级为了AVKit,而这个问题是这个[PR](https://github.com/opencv/opencv/pull/7159)已经解决了，但是还没有发布版本，因此需要在安装中指定为使用--HEAD.
>brew -v install --HEAD opencv3

安装过程中有出现了另一个错误：
```
/tmp/opencv3-20161203-50490-128l1si/opencv-3.1.0/modules/videoio/src/cap_qt.cpp:63:10: fatal error: 'QuickTime/QuickTime.h' file not found
#include <QuickTime/QuickTime.h>
         ^
1 error generated.
make[2]: *** [modules/videoio/CMakeFiles/opencv_videoio.dir/src/cap_qt.cpp.o] Error 1
make[1]: *** [modules/videoio/CMakeFiles/opencv_videoio.dir/all] Error 2
make: *** [all] Error 2
READ THIS: https://git.io/brew-troubleshooting
```

最终使用的命令为:
>brew -v install --with-contrib --with-cuda --with-ffmpeg --with-gphoto2 --with-gstreamer --with-java --with-libdc1394 --with-opengl --with-openni2 --with-python3 --with-qt5 --with-tbb --with-vtk --HEAD opencv3

