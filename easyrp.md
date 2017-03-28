>brew install cmake

https://github.com/opencv/opencv/pull/7159 AVFoundation support for macOS


```
/tmp/opencv3-20161203-50490-128l1si/opencv-3.1.0/modules/videoio/src/cap_qt.cpp:63:10: fatal error: 'QuickTime/QuickTime.h' file not found
# include <QuickTime/QuickTime.h>
         ^
1 error generated.
make[2]: *** [modules/videoio/CMakeFiles/opencv_videoio.dir/src/cap_qt.cpp.o] Error 1
make[1]: *** [modules/videoio/CMakeFiles/opencv_videoio.dir/all] Error 2
make: *** [all] Error 2

READ THIS: https://git.io/brew-troubleshooting

```


>brew -v install --with-contrib --with-cuda --with-ffmpeg --with-gphoto2 --with-gstreamer --with-java --with-libdc1394 --with-opengl --with-openni2 --with-python3 --with-qt5 --with-tbb --with-vtk --HEAD opencv3


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

```
Please migrate your Casks to the new location and delete /opt/homebrew-cask/Caskroom,
or if you would like to keep your Caskroom at /opt/homebrew-cask/Caskroom, add the
following to your HOMEBREW_CASK_OPTS:

  --caskroom=/opt/homebrew-cask/Caskroom
```

where is ncvv 
```
/usr/local/cuda/bin/
```
```
/Developer/NVIDIA/CUDA-8.0/bin
```


