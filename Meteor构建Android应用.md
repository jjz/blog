# Meteor构建Android应用
##首先要安装Meteor 
[安装Meteor][1]

##在Meteor中安装Android SDK
>meteor install-sdk android

    注意下载Android的build-tools的时候，因为众所周知的原因，你需要翻墙才能下载。
    ✓ Found Android bundle
    ✓ A JDK is installed
    ✓ Found Android Platform tools
    ✓ Found Android Build Tools
    ✓ Found Android 19 API
    ✓ Found suitable Android x86 image
    ✓ 'meteor' android virtual device (AVD) found
    ✓ Android emulator acceleration is installed

##对Meteor App添加对Android的支持
>meteor add-platform android
中间会询问你是否同意协议，直接`Y`
    android: added platform
该项目现在已经支持android了

##在Android上运行Meteor

>meteor run android 

会安装`Cordova`插件

    Cordova提供了一组设备相关的API，通过这组API，移动应用能够以JavaScript访问原生的设备功能，如摄像头、麦克风等。

meteor会建一个虚拟机，在上面运行APP
![虚拟机效果图][2]


>meteor run android-device
使用这个命令可以让APP在真机上面运行
![真机效果图][3]  


  [1]: http://segmentfault.com/a/1190000003725901
  [2]: /img/bVpNYl
  [3]: /img/bVpNYs



