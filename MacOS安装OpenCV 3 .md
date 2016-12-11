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

安装过程中有出现了的另一个错误：
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

可以尝试安装命令:
>brew -v install --with-contrib  --with-ffmpeg --with-gphoto2 --with-gstreamer --with-java --with-libdc1394 --with-opengl --with-openni2 --with-python3 --with-qt5 --with-tbb --with-vtk --HEAD opencv3

##CUDA
CUDA（Compute Unified Device Architecture）是NVIDIA推出的运算平台，是一种并行计算架构,该架构使GPU能够解决复杂的计算问题,如果我们想使用CUDA和OpenCV混合编程，需要适应CUDA重新编译OPENCV：
>brew -v --with-cuda install --HEAD opencv3

如果没有安装CUDA的会得到提示：
```
opencv3: CUDA is required.To use this formula with NVIDIA graphics cards you will need to
download and install the CUDA drivers and tools from nvidia.com.

    https://developer.nvidia.com/cuda-downloads

Select "Mac OS" as the Operating System and then select the
'Developer Drivers for MacOS' package.
You will also need to download and install the 'CUDA Toolkit' package.

The `nvcc` has to be in your PATH then (which is normally the case).

CudaRequirement unsatisfied!

You can install with Homebrew-Cask:
  brew cask install cuda

```
使用命令`brew cask install cuda`按照提示可以完成CUDA的安装。

另外在这里记录下CUDA在Ubuntu上安装的方法,进入[https://developer.nvidia.com/cuda-downloads](https://developer.nvidia.com/cuda-downloads)，选择Linux，再选择平台，Linux发行版，以及版本就可以出现是从网络上面下载安装，还是从本地安装的选项:
 
 * 网络安装,先下载deb安装包:
>wget http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/cuda-repo-ubuntu1604_8.0.44-1_amd64.deb
运行命令安装：
>sudo dpkg -i cuda-repo-ubuntu1604_8.0.44-1_amd64.deb
>sudo apt-get update
>sudo apt-get install cuda

* 本地安装，需要先下载完整的deb安装包:
>wget http://developer.download.nvidia.com/compute/cuda/8.0/secure/prod/local_installers/cuda-repo-ubuntu1604-8-0-local_8.0.44-1_amd64.deb?autho=1481416577_ed84fa072b0f3fb5f814762777672f7e&file=cuda-repo-ubuntu1604-8-0-local_8.0.44-1_amd64.deb
运行命令安装：
>sudo dpkg -i cuda-repo-ubuntu1604-8-0-local_8.0.44-1_amd64.deb
>sudo apt-get update
>sudo apt-get install cuda


