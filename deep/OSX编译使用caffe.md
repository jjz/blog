# MacOS编译安装Caffe
`Caffe`是一个清晰而高效的深度学习框架，是纯粹的C++、CUDA架构，支持命令行，Python和MATLAB接口，可以在CPU和GPU直接无缝切换，Caffe的优势:

1.  上手快,模型和相应优化都是以文本形式而非代码形式给出,`Caffe`给出了模型的定义，最优化设置以及预训练的权重，方便立即上手。
2.  速度快，Caffe与cuDNN结合使用，能够运行最棒的模型和海量的数据。
3.  模块化，方便拓展新的认知和设置.
4.  开源，开放

`Caffe`在MacOS可以使用`Homebrew`安装，也是自行编译源代码安装，这里介绍下直接编译源代码安装.
## 下载Caffe源代码
`Caffe`的github地址：[https://github.com/BVLC/caffe](https://github.com/BVLC/caffe)，直接克隆Caffe源代码:
>git clone git@github.com:BVLC/caffe.git

复制`Makefile.config`文件：
>cd caffe
>cp Makefile.config.example Makefile.config

`Makefile.config`文件中有些编译选项需要做更改才能在MacOS上面编译通过，后面会介绍,下面先介绍下编译Caff所需要的依赖。
## 安装Caffe依赖
编译`Caffe`需要有大量的依赖,开始扁你之前，需要先安装这些依赖。
(1)  使用GPU模式需要安装CUDA,安装CUDA的命令：
>brew cask install cuda

也可以不采用GPU模式，只使用CPU，在`Makefile.config`中做修改：
>CPUU_ONLY=1

(2)  Boost
`Caffe`使用的是c++开发，如果要使用python调用`Caffe`的接口的话,需要安装boot.python:
>brew install boost --with-python
>brew install boost-python

(3)  OpenCV 安装,直接安装OpenCV
> brew install opencv
 
 OpenCV安装之后需要在`Makefile.config`中设置OpenCV的文件头路径，以及lib的路径：
 		
 	INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include /usr/local/Cellar/opencv/2.4.13.2/include
	 LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib /usr/local/Cellar/opencv/2.4.13.2/lib

(4)  数据库leveldb,lmdb,hdf5安装:
>brew install leveldb
>brew install lmdb
>brew tap homebrew/science
>brew install install homebrew/science/hdf5

(5)  日志与数据操作
> brew install protobuf
> brew install glog
> brew install gflags
> brew install snappy

## 安装caffe-python依赖
先要安装 Python依赖库：numpy,h5py以及scikit-image
>brew install numpy
>pip install h5py
>pip install scikit-image

安装完Python的依赖类库之后需要注意以下的4点:

1. 设置`Makefile.config`中numpy的路径：
 > PYTHON_INCLUDE := /usr/include/python2.7 \
    /usr/local/Cellar/numpy/1.12.0/lib/python2.7/site-packages/numpy/core/include
2. 在python中使用OpenCV,需要把OpenCV安装目录下`../python/site-packages`里面的两个文件`cv.py`和`cv2.so`拷贝到`/usr/local/lib/python2.7/site-packages`目录下，这样python才能调用OpenCV。
3. 在`Makefile.config`设置`WITH_PYTHON_LAYER:=1`
4. 在`Makefile.config`中设置PYTHON_LIB:
>PYTHON_LIB := /usr/local/Cellar/python/2.7.13/Frameworks/Python.framework/Versions/2.7/lib



## 编译Caffe
使用`make`命令编译Caffe: 
>make clean
>make all 
>make test
>make runtest

编译Caffe-Python:
>make pycaffe

以上编译都通过之后将caffe/python添加到python系统路径里`fish`设置命令:
>set -gx PYTHONPATH  path/to/caffe/python $PYTHONPATH

`bash`的设置命令：
>export PYTHONPATH=path/to/caffe/python:$PYTHONPATH

## 使用Caffe
在命令行中直接测试`Caffe`是否编译成功。
>python
>import caffe

没有错误出现则表示`Caffe`安装成功。



