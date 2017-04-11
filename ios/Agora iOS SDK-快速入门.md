#Agora iOS SDK-快速入门
最近有一款应用很火，叫`Housparty`，Housparty是一款视频群聊应用，最近它在App Store上的下载排名已经超过了Facebook。同时，有媒体称Houseparty已经完成了最新一轮的投资。自己就想着模仿一个玩玩，研究了下，发现有个叫`Agora`的可以实现部分的功能，就想试用下。
`声网Agora.io`是一家提供稳定，高可用，有质量保障的实时`视频通话`和实时`全互动直播技术`服务的平台，支持全平台，只需要简单集成SKD，即可让应用实现高清视频通话，和多主播实时全互动直播。
既然已经有了SDK，就不需要自己写视频以及通信部分的代码了，这样写一个`Housparty`的demo就更简单了，下面以iOS平台为例，看下如何集成`Agora SDK`。
## 环境准备
本文使用的环境如下:
* XCode 8.3.1
* 最小SDK iOS 9.3
* 真机
* Swift语言
* [最新Agora SDK ](http://www.agora.io/cn/news/download/)
* [申请AppID](https://dashboard.agora.io/signin)

先要注册`agora.io`，注册完成之后再新建一个项目，需要拿到对应的`App ID`。后面的示例代码需要使用该`App ID`。
最新的`Agora SDK`中有两个文件夹：
* ./libs 中含有的是所需的库（集成到App需要的）
* ./samples 包含Open Video Call 和 Open Live的代码示例

需要的环境准备好之后，新建一个`agora`项目，就可以开始配置和集成`Agora SDK`。
## 添加SDK
先把Agora SDK中`./libs`复制到agora项目中的agora目录下。再把Agora SDK添加到项目的`Libraries`中。
添加`Libraries`的方法：

1. 选中当前Target(agora)
2. Build Phases 
3. Link Binary With Libraries
4. 点击+

![Link Binary](http://upload-images.jianshu.io/upload_images/22188-ee97d1026cc20fd3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/540)
这个时候出现的界面是添加系统类库的，选择`Add Other ...`从项目目录中选中`./libs`添加里面的内容到`Libraries`中。这样就添加了`Agora SDK`。
在添加`Agora SDK`之后，还需要添加`Agora SDK`所依赖的库，参考官网上面demo使用的`libraries`，添加以下类库:
* CoreTelephony.framework
* CoreMedia.framework
* VideoToolbox.framework
* AudioToolbox.framework
* AVFoundation.framework
* libc++.tbd

`build`的时候出现错误:
```
Showing All Messages
  "_res_9_getservers", referenced from:
      agora::commons::network::get_dns_list(bool) in AgoraRtcEngineKit(libmediasdk.a-arm64-master.o)
  "_res_9_ninit", referenced from:
      agora::commons::network::get_dns_list(bool) in AgoraRtcEngineKit(libmediasdk.a-arm64-master.o)
  "_res_9_ndestroy", referenced from:
      agora::commons::network::get_dns_list(bool) in AgoraRtcEngineKit(libmediasdk.a-arm64-master.o)
ld: symbol(s) not found for architecture arm64
clang: error: linker command failed with exit code 1 (use -v to see invocation)

```
运行官网的demo也出现了同样的错误，说明类库没有导入完整，还需要导入类库是`libresolv.9.tbd`。
类库依赖问题比较容易出现，另一方面也说明没有支持`Pod`的麻烦，建议`Agora`能尽快支持`CocoaPods`。
再次`build`又出现了个问题:
```
ld: library not found for -lcrypto
```
问题的原因是：编译时找不到需要的链接库导致的，解决方法:
1. 选中当前Target(agora)
2. Build Settings 
3. Search Paths 
4. Library Search Paths 
5. 新增`"${PROJECT_DIR}/agora/libs"`

![Library Search Paths](http://upload-images.jianshu.io/upload_images/22188-85eabf9a96c231fd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/540)
另外`Agora SDK`并不支持**bitcode**，因此需要把bitcode禁止掉，禁止bitcode的方法： 
1. 选中当前Target(agora) 
2. Build Settings  
3. Build Options 
4. Enable Bitcode ->No

![bitcode](http://upload-images.jianshu.io/upload_images/22188-3d974f0d0dc945ce.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/540)

这样就配置完成了`Agora SDK`的支持，下面就可以开始使用`Agora SDK`了。
## 访问OC类库
`Agora SDK`使用的是`Object-c`开发的，而我们的项目使用的是`Swift`开发的，因此就需要在`Swift`中访问`OC的类库`，新建一个文件命名为`agora-Bridging-Header.h`,在该文件中引入`Agora SDK`:
```
#import <AgoraRtcEngineKit/AgoraRtcEngineKit.h>
#import <AgoraRtcCryptoLoader/AgoraRtcCryptoLoader.h>
```
然后把该文件设置为`Objective-C Bridging Header`，设置方法：
1. 选中当前Target(agora)
2. Build Settings 
3. Swift Compiler-General
4. Objective-C Bridging Header 
5. agora/agora-Bridging-Header.h

![oc bridging](http://upload-images.jianshu.io/upload_images/22188-d25db5ad9d6394be.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/540)
这样能在`Swift`中使用`Agora SDK`了。
在开始调用`Agora SDK`之前还要知道，`Agora SDK`实现的是一套高清视频通话或直播系统，除了完成集成`Agora SDK`的工作之外，还需要给项目对应的访问权限。
## 权限
需要给项目两个权限才能使用`Agora SDK`，这两个权限是:
* 相机
* 麦克风

在`info.plist`添加这两个权限:
![info.plist](http://upload-images.jianshu.io/upload_images/22188-eb6701d559a4a3f2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/540)
具体设置的内容为:
* `Privacy - Camera Usage Description`设置为:`use camera to start video call`
* `Privacy - Microphone Usage Description`设置为`use microphone to start video call`

这样完成了`Agora SDK`的项目配置，后面将继续写如何使用`Agora SDK`，包含的功能：

1. 创建room
2. 分屏，2分屏、4分屏、6分屏
3. 窗口切换
4. 前后摄像头切换

项目地址:[https://github.com/jjz/agora-swift](https://github.com/jjz/agora-swift)