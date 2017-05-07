在上一篇[Agora iOS SDK-快速入门](http://www.jianshu.com/p/bdf272b3505c)中聊了如果配置Agora iOS SDK，这一篇将看下如何使用Agora如何进行聊天。
Agora封装了视频聊天的大多数常用功能，直接调用Agora API即可直接开始聊天。这篇文章的主要目标是结合文档完成一对一视频聊天的Demo。
## 初始化
`AgoraRtcEngineKit`是Agora SDK的入口，通过它就可以完成聊天的基本设置。比如设置远程视频、本地视频的配置、声音控制、以及设置摄像头等。
首先，新建一个`ChatViewController`用来实现聊天功能，在`ChatViewController`中声明一个AgoraRtcEngineKit的变量:
```swift
var agoraKit : AgoraRtcEngineKit!
```
初始化该变量需要实现委托:`AgoraRtcEngineDelegate`,它是AgoraRtcEngineKit的回调，在出现错误、离开频道....等情况出现的时候可以在该回调中得到通知。
它的其中一个方法`- (void)rtcEngine:(AgoraRtcEngineKit *)engine firstRemoteVideoDecodedOfUid:(NSUInteger)uid size:(CGSize)size elapsed:(NSInteger)elapsed;`的意思是在第一个用户准备好视频通信的情况下会触发该方法，这个时候就可以配置该用户的显示界面了，`AgoraRtcEngineDelegate`实现：
```
extension ChatViewController:AgoraRtcEngineDelegate{
    func rtcEngine(_ engine: AgoraRtcEngineKit!, firstRemoteVideoDecodedOfUid uid: UInt, size: CGSize, elapsed: Int) {
     
        
    }
    func rtcEngine(_ engine: AgoraRtcEngineKit!, didLeaveChannelWith stats: AgoraRtcStats!) {
        
    }

}

```
这里使用了`extension`扩展了ChatViewController用来实现`AgoraRtcEngineDelegate`。
再实现了委托之后就可以实例化agoraKit了，实例化方法：
>   agoraKit=AgoraRtcEngineKit.sharedEngine(withAppId: AgoraSetting.AgoraAppId, delegate: self)

这样就完成了AgoraRtcEngineKit的初始化，初始化完成之后还需要设置视频显示，下面就先从远程视频的设置开始。

## 开启远程用户视频
远程视频的设置也比较简单，在`AgoraRtcEngineDelegate`中的方法`func rtcEngine(_ engine: AgoraRtcEngineKit!, firstRemoteVideoDecodedOfUid uid: UInt, size: CGSize, elapsed: Int)`被调用的时候，就可以开启该远程视频的显示，实现该方式就可以拿到该用户的信息。
在设置远程用户视频之前，还需要新建一个UIView用来显示远程视频，远程视频的显示会在该UIView内完成，新建一个UIView用来接收远程视频:
>@IBOutlet weak var remoteView: UIView!

还需要一个VideoCanvas的实例，在该实例中配置远程视频的显示方式。
>let videoCanvas = AgoraRtcVideoCanvas()

videoCanvas需要设置下下面几个参数：
* uid 用来区分用户的唯一标识
* view 用来设置显示远程视频的view
* renderMode 视频显示模式包括三种模式：AgoraRtc_Render_Hidden、AgoraRtc_Render_Fit、AgoraRtc_Render_Adaptive

配置好videoCanvas之后，就可以在agoraKit中开启远程视频了：
>agoraKit.setupRemoteVideo(videoCanvas)

使用Agora设置远程视频的播放就是如此简单，不用再关心底层的实现，减少了应用开发者在底层上的开发时间。当然本地视频的设置也是如此的简单。

## 本地视频
对于本地视频的配置，首先需要设置视频参数，包括分辨率、帧率、码率等，当设置的分辨率不被摄像头支持的时候，SDK会自动找到一个合适的分辨率来适配摄像头，但显示的仍然是指定的分辨率。
设置本地视频配置的方法：
>setVideoProfile:(AgoraRtcVideoProfile)profile
    swapWidthAndHeight:(BOOL)swapWidthAndHeight;

第一参数包含了分辨率、帧率、码率的配置，在SDK中已经有已经设置好的参数，在demo中使用的是`._VideoProfile_360P`，第二个参数表示是否交换宽和高，用来适应横屏和竖屏的显示。默认为false。
设置本地视频配置：
>agoraKit.setVideoProfile(._VideoProfile_360P, swapWidthAndHeight: false)

和远程视频的设置一样，首先需要一个view用来接收本地视频的显示，定义一个本地显示本地视频的view：
>@IBOutlet weak var localVideo: UIView!

本地视频的显示也需要实例化一个`AgoraRtcVideoCanvas`，还要配置AgoraRtcVideoCanvas的三个参数，然后把`AgoraRtcVideoCanvas`设置给agoraKit，完整代码如下：
```swift
func setupLocalVideo(){
        agoraKit.setVideoProfile(._VideoProfile_360P, swapWidthAndHeight: false)
        let videoCanvas=AgoraRtcVideoCanvas()
        videoCanvas.uid=0
        videoCanvas.view=localVideo
        videoCanvas.renderMode = .render_Adaptive
        agoraKit.setupLocalVideo(videoCanvas)
    }
```
上面完成了本地视频和远程视频的设置和显示，在需要和人一起聊天之前，还要两个人都加入到一个频道中。
## 加入一个频道
`频道`：在同一个频道内的用户可以互相通话，如果多个用户加入了一个频道就可以群聊，一个用户只能加入一个频道。切换频道必须从当前频道中退出。
先看加入频道的代码：
```swift
 func joinChannel(){
        agoraKit.joinChannel(byKey: nil, channelName: "demo", info: nil, uid: 0){[weak self](sid,uid,elapsed)->Void in
            if let weakSelf = self{
                weakSelf.agoraKit.setEnableSpeakerphone(true)
                UIApplication.shared.isIdleTimerDisabled = true
            }
            
    }
```
下面简单说下各个参数的含义：
* byKey 可选参数，使用nil或者App ID都可以，如果对于安全要求极高的话可以使用申请Channel Key
* channelName 频道名称
* info 开发可以附件信息，该信息不会给用户看到
* uid 用户唯一标识
* joinChannelSuccessBlock 一个加入成功的回调block,在加入频道成功之后通过设置isIdleTimerDisabled来阻止用户锁屏。

通过`agoraKit.leaveChannel()`可以离开频道，只有离开一个频道才能进入下一个频道，leaveChannel是异步操作，调用时并没有真正的退出频道，在真正的退出频道后，会触发didLeaveChannelWithStats回调。
在一个频道中的用户就可以正式的开始聊天了。
在聊天过程中特别是会议聊天时，有时需要禁止自己的声音，防止打扰别人说话，Agora SDK也提供了对于声音和摄像头的控制。
## 声音控制
聊天中对于声音的控制有很多种方式，下面介绍下几种比较常用的方式：
#### 声音开关
`muteLocalAudioStream `可以设置本地声音的开关,使用方式也比较简单，通过一个Button控制本地声音的开启:
```swift
@IBAction func mute(_ sender: UIButton) {
        sender.isSelected = !sender.isSelected
        agoraKit.muteLocalAudioStream(sender.isSelected)
    }

```
而`muteAllRemoteAudioStreams `的作用是禁止所有的远程视频的声音，使用方式和`muteLocalAudioStream`一样。
如果想禁止某一个用户的声音可以可以使用方法：
```
-(int)muteRemoteAudioStream:(NSUInteger)uid muted:(BOOL)muted;
```

其中uid是用户的唯一标识，利用uid就可以针对某一个用户开启/关闭该用户的声音。
#### 开启扬声器
通过方法：
```
-(int)setEnableSpeakerphone:(BOOL)enableSpeaker;
```
可以设置使用扬声器或听筒，其中YES是输出声音到扬声器，NO是使用听筒。

#### 监听声音
在多人聊天的过程中，我们还需要知道是谁在说话，这个时候就需要设置监听用户的声音状态，通过方法：
```
-(int)enableAudioVolumeIndication:(NSInteger)interval smooth:(NSInteger)smooth;
```
就可以监听远程用户的声音状态，设置该方法之后可以在`AgoraRtcEngineDelegate`中的reportAudioVolumeIndicationOfSpeakers中收到谁在说话以及他说话的音量。

上面是针对声音的方法，更多的设置可以参考官方的文档，下面再看下针对摄像头的方法
## 摄像头
对于摄像头的控制方法也有很多的方式，下面以几个比较常用方法为主简单介绍下。
#### 开启本地预览
在demo中的频道列表中使用的背景是本地视频的预览，本地预览相关的有两个方法：
> startPreview(开启预览)
> stopPreview(停止预览)

注意开启视频预览之前必须先设置本地的视频显示属性以及预览的UIView，详细的设置可以参考demo。

#### 摄像头切换
视频聊天中常常需要对前置/后置摄像头进行切换，切换摄像头的代码为：
```swift
 @IBAction func switchCamera(_ sender: UIButton) {
        agoraKit.switchCamera()
    }
```
使用该方法，SDK会判断当前摄像头的状态，并对摄像头进行切换。
#### 视频开关
和声音开关类似，也可以通过API暂停发送视频，暂定发送本地视频流的方法:
```
-(int)muteLocalVideoStream:(BOOL)mute;
```
暂停所有远程视频流的方法:
```
-(int)muteAllRemoteVideoStreams:(BOOL)mute;
```
暂停某一个远程用户视频的方法：
```
-(int)muteRemoteVideoStream:(NSUInteger)uid mute:(BOOL)mute;
```
通过该例子可以实现一对一的视频聊天，下一篇文章将介绍下如果进行多人视频聊天。


源代码地址:[https://github.com/jjz/agora-swift](https://github.com/jjz/agora-swift)
参考文档:[https://docs.agora.io/cn/user_guide/API/ios_api.html](https://docs.agora.io/cn/user_guide/API/ios_api.html)

