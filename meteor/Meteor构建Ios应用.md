在之前的文章[Meteor构建Android应用](http://www.jianshu.com/p/aa5d99401764) 中介绍了如何使用`Meteor`构建Android应用，这篇文章中我们看如何使用`Meteor`构建ios应用。
关于如何安装`Meteor`可以参考这篇文章[http://www.jianshu.com/p/183185d2b9b1](http://www.jianshu.com/p/183185d2b9b1)。

## 安装ios SDK
使用命令直接安装ios的SDK：
>meteor install-sdk ios

```
 ✓ Xcode is installed
✓ Xcode license agreed
```
`Meteor`会检查你的系统中是否安装了`Xcode`,如果没有安装，`Xcode`会引导你安装Xcode。
## 添加对IOS的支持
使用命令行直接让你的项目直接ios平台:
>meteor add-platform ios

```
 ios: added platform
```
现在你的项目已经支持ios平台了，可以直接运行了！

## 运行

使用命令直接运行ios虚拟机:
>meteor run ios 

会安装提示安装`Cordova`插件:

>    Cordova提供了一组设备相关的API，通过这组API，移动应用能够以JavaScript访问原生的设备功能，如摄像头、麦克风等。
    
虚拟机运行效果图:
![虚拟机运行](http://upload-images.jianshu.io/upload_images/22188-2f45f4f38cc99412.png?imageMo
![Uploading 2080928388-55f044996ef8d_articlex_871500.png . . .]
gr2/auto-orient/strip%7CimageView2/2/w/1240)

在真机上运行:
>meteor run ios-device

使用这个命令可以让`Meteor`在ios真机上面运行。
在真机上面运行，Meteor需要先构建一个`Xcode`项目，直接在这个项目中选择你要运行的机器即可:
![xcode项目](http://upload-images.jianshu.io/upload_images/22188-3c344f6b6ab1c457.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

真机运行效果:

![真机运行效果图](http://upload-images.jianshu.io/upload_images/22188-3630255d0772c08c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


