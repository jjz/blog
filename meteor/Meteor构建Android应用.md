[安装Meteor](http://www.jianshu.com/p/183185d2b9b1)，就可以直接构建应用了，`Meteor`可以直接Android，IOS应用，桌面应用以及Web应用。
先从构建Android应用开始.

## 安装Android SDK
直接安装`Meteor`上面的`Android SDK`.
>meteor install-sdk android

```
    注意下载`Android SDK`的`build-tools`的时候，因为众所周知的原因，你需要翻墙才能下载。
    ✓ Found Android bundle
    ✓ A JDK is installed
    ✓ Found Android Platform tools
    ✓ Found Android Build Tools
    ✓ Found Android 19 API
    ✓ Found suitable Android x86 image
    ✓ 'meteor' android virtual device (AVD) found
    ✓ Android emulator acceleration is installed
```

## 添加对Android的支持
在原有的应用上面直接添加对于`Android`的支持。
>meteor add-platform android

中间会询问你是否同意协议，直接选择`Y`
```    
android: added platform
```
该项目现在已经支持Android 平台了

## 在Android上运行Meteor

>meteor run android 

这是`Meteor`运行到虚拟机的命令。
当运行的时候会需要安装`Cordova`插件:
```
    Cordova提供了一组设备相关的API，通过这组API，移动应用能够以JavaScript访问原生的设备功能，如摄像头、麦克风等。
```
`Meteor`会新建一个虚拟机，在上面运行APP效果图:

![App效果图](http://upload-images.jianshu.io/upload_images/22188-9155269efa386af7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


使用真机运行的命令：
>meteor run android-device

真机运行效果图：

![真机运行](http://upload-images.jianshu.io/upload_images/22188-57ca0538b7d2644f.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这样我们可以使用`Meteor`开发Android应用了。

