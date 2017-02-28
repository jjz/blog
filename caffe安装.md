1. CUDA is required for GPU mode 
>CPUU_ONLY=1
2. opencv 安装
3. Makefile.config 添加opencv
4. brew install 
5. numpy 路径
>PYTHON_INCLUDE := /usr/include/python2.7 \
    /usr/local/Cellar/numpy/1.12.0/lib/python2.7/site-packages/numpy/core/include


6. make 
>make clean
>make all 
>make test
>make runtest
>make pycaffe

7. 添加caffe/python 到python 系统路径依赖
8. import 崩溃
 	WITH_PYTHON_LAYER:=1 https://github.com/BVLC/caffe/issues/591

