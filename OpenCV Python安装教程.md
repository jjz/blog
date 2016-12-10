[OpenCV](http://opencv.org/)全称:Open Source Computer Vision Library，是一个开源的跨平台计算机视觉库，github地址：[https://github.com/opencv/opencv](https://github.com/opencv/opencv)。OpecCV主要用c++语言编写，也提供了python等其他语言的，下面介绍下如何在MacOS和Ubuntu上如何安装OpenCV以及OpenCV的Python调用库。
##MacOS上面安装
####安装OpenCV
使用Homebrew安装直接安装OpenCV:
>brew install opencv

OpenCV的安装目录为：`/usr/local/Cellar/opencv/`。
也可以从[官网](http://opencv.org/)下载安装包直接安装。比较复杂的是下载源代码使用`cmake`安装。
###cv2

OpenCV在Python中调用的时候使用的库是`cv2`。在python中可以直接使用`cv2`:
>import cv2

直接`import`会出现错误：
```
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ImportError: No module named cv2
```
这是因为Python并不能找到对应的OpenCV的库。
那`cv2`在那里呢?使用`pip install`安装，也没有找到对应的cv2库。回头看OpenCV的安装目录在`lib`下面可以看到一个`python2.7`的包，Python的相关引用都在里面，在`site-packages`目录下有两个文件:
```
cv.py
cv2.so
```
可以把这两个文件复制Python库目录`/usr/local/lib/python2.7/site-packages`下面，这样在调用的时候Python就可以找到`cv2`的库。

##Ubuntu上安装OpenCV的方法
先Ubuntu上可以直接编译OpenCV安装,首先要安装编译需要的依赖包：
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

另外在Ubuntu上使用Python调用OpenCV需要安装对应的python包:
>sudo apt-get install python-opencv

完整脚本地址：[https://github.com/jjz/script/blob/master/opencv-python.sh](https://github.com/jjz/script/blob/master/opencv-python.sh)
###使用OpenCV
一个简单用来验证Python是否能够调用OpenCV的方法:
>import cv2
>cv2.__version__

可以得到OpenCV版本：
```
'2.4.13.1'
```

