#Google VR 开发简介
Google的VR有两个virtual reality(VR)的平台，一个是Cardboard,一个廉价的VR解决方案，2015年Goolgle的I/O推出。另一个就是今年的I/O大会刚刚退出的Daydream,期待着Daydream能够带来更加优质的，性价比高的VR解决方案。
Cardboard:
Daydream:

Google有专门的VR的delelopers页面：https://developers.google.com/vr/。包括样例代码，API文档，设计规范等。

Google VR有三个SDK：Unity 3D，Android,IOS，他们同样的都支持Cardboard和Daydream

##Android
Android平台有Java版本对应的SDK，和c/c++对应的NDK.
熟悉OpenGL的开发者能够快速的使用Google VR SDK创建VR程序，而且可以Android studio直接开发。
可以做到：

* 校正镜头
* 立体声音
* 头部跟踪
* 3D校准
* 并排呈现
* 立体几何
* 输入事件处理

Android SDK: https://developers.google.com/vr/android/get-started#treasure_hunt_sample_app
Android NDK: https://developers.google.com/vr/android/ndk/get-started

##IOS
Google VR 的IOS SDK是采用Objective-c编写的。当然需要在XCode中运行，IOS平台上面也提供了对应的CocoaPod类库.
>pod CardboardSDK
同样的在Android的SDK上面能够处理的事情，使用IOS的SDK同样可以做到。
IOS SDK：https://developers.google.com/vr/ios/get-started
##Unity 3D
Unity 3D是三维动画的开发工具，Google VR 有对应的Unity 3D下的SDK.
Unity SDK支持构建Android下的Daydream和Cardboard，而在IOS设备上面目前还不支持使用Unity 3D开发Daydream。
特点:

* 提供的插件和组件让开发变得更容易
* 可以从头开始一个VR Unity项目
* 当前的Unity 3D项目转向VR
* 让应用容易的切换VR模式
Ggoole VR在stackoverflow的专门问答区：http://stackoverflow.com/questions/tagged/


