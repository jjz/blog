[OpenCV](http://opencv.org/)全称:Open Source Computer Vision Library，是一个开源的跨平台计算机视觉库，github地址：[https://github.com/opencv/opencv](https://github.com/opencv/opencv)。OpecCV主要用c++语言编写，也提供了python语言的接口。
##在MacOS上面可以直接用python调用OpenCV。

###安装OpenCV
使用Homebrew安装OpenCV:
>brew install opencv

OpenCV的安装目录为：`/usr/local/Cellar/opencv/`。
也可以从[官网](http://opencv.org/)下载安装包直接安装。
###cv2

OpenCV在python中使用的库为`cv2`。在python中可以直接使用`cv2`:
>import cv2
```
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ImportError: No module named cv2
```
而`cv2`在那里安装呢?使用`pip install`，根本就找不到对应的cv2库。再回到OpenCV的安装目录在`lib`下面可以看到一个`python2.7`的包，python的相关引用都在里面，在`site-packages`下面有两个文件:
```
cv.py
cv2.so
```
把OpenCV中的python相关文件放到$PATH环境变量中，python就可以自动加载`cv2`库。



##在Ubuntu上安装OpenCV的方法
先安装编译需要的依赖包：
>sudo apt-get install cmake  build-essential  libgtk2.0-dev libjpeg8-dev  libjpeg-dev  libavcodec-dev libavformat-dev  libtiff5-dev cmake libswscale-dev 
下载OpenCV的源代码:
>wget https://codeload.github.com/opencv/opencv/tar.gz/2.4.13

解压:
>tar -xzvf 2.4.13.tar.gz
编译并安装:
>cd opencv-2.4.13
cmake
make
sudo make install

在Ubuntu上使用python调用OpenCV需要安装对应的python包:
>sudo apt-get install python-opencv

###使用OpenCV
一个简单的使用cv2的方法:
>import cv2
>cv2.__version__
可以得到OpenCV的版本输出：
```
'2.4.13.1'
```



