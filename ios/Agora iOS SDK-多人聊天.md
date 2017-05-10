在上一篇[Agora iOS SDK-开始聊天](http://www.jianshu.com/p/baacf2c5ad6f)介绍了如何使用Agora SDK进行一对一的聊天，这篇主要介绍下如何使用Agora iOS进行多人聊天，需要实现的功能：
1.  随着加入人数的变化，而显示不同的UI，主要是分屏
2. 在多屏显示的情况下，点击一个小窗，会放大显示该聊天窗
3. 前篇实现的聊天功能

实现上面所说的功能:`分屏`，最好的方式是使用瀑布流布局，这样可以满足分屏的需要。
## 瀑布流
分屏显示最好的方式是采用瀑布流，这样比较方便的能够适应UI的变化。
瀑布流的实现方式比较多，常用的方式是使用`UICollectionView`实现，自定义一个`UICollectionViewLayout`。UICollectionViewLayout是向UICollectionView提供布局信息，也包括对于视图的布局信息。需要重载UICollectionViewLayout的以下方法：
* collectionViewContentSize 返回内容的大小
* (UICollectionViewLayoutAttributes *)layoutAttributesForItemAtIndexPath:(NSIndexPath *)indexPath  根据位置反馈cell对应的布局属性
* (NSArray<UICollectionViewLayoutAttributes *> *)layoutAttributesForElementsInRect:(CGRect)rect  ，Cell的布局方式
*  prepareLayout 初始化方法

具体的实现可以参考[https://github.com/LD1314/LDWaterflowLayout](https://github.com/LD1314/LDWaterflowLayout)，该demo中也会使用该实现。
有了一个已经实现的瀑布流之后，下面就可以实现分屏了。需要引入`LDWaterflowLayout`。
## 动态聊天窗
新建一个ViewController类，命名为`MutilChatViewController`，用来做分屏显示的需求，在Agora SDK中一个远程视频的显示只和该用户的uid有关，所以使用的数据源只需要简单定义为包含uid即可，定义为：
```
public var dataArray:Array<UInt>?=Array<UInt>();
```
在Agora的委托`AgoraRtcEngineDelegate`中当一个用户加入聊天之后会触发它的一个方法，实现该方法，把用户的uid加入dataArray中:
```swift
func rtcEngine(_ engine: AgoraRtcEngineKit!, didJoinedOfUid uid: UInt, elapsed: Int) {
        if(!(dataArray?.contains(uid))!){
            dataArray?.append(uid)
            collectionView.reloadData()
        }
        
    }
```

对于瀑布流的实现需要MutilChatViewController继承:
* UIViewController
* UICollectionViewDataSource
* LDWaterflowLayoutDelegate

其中`LDWaterflowLayoutDelegate`就是瀑布流的委托，重写它的两个方法就可以实现分屏:
1. columnCount(in waterflowLayout: LDWaterflowLayout!) -> CGFloat 
该方法用来指定一行显示几个元素，这里采用比较简单的方式当只有一个1个用户的时候就显示在一行，2个用户的时候就在一行显示2个元素...4个用户的时候就显示为2行...
2. waterflowLayout(_ waterflowLayout: LDWaterflowLayout!, heightForItemAt index: UInt, itemWidth: CGFloat) -> CGFloat 
该方法用来设定每个元素的高度，也是采用比较简单的方式，只用1行的时候高度为300，有2行的时候高度为150，3行的时候高度为100。

把分屏的布局写好之后，就可以在每一个`UICollectionViewCell`上播放聊天视频了。
## 播放聊天视频
新建一个类ChatCell它继承了UICollectionViewCell，在ChatCell中有两个组件：` videoView: UIView!`和`labelUser: UILabel!`，前者用来显示用户视频，后者用来显示用户信息，videoView布局在整个ChatCell上，随着ChatCell的变化而变化。
要在ChatCell中播放聊天视频，必须在ChatCell持有`AgoraRtcEngineKit`的变量，因此需要ChatCell声明AgoraRtcEngineKit的变量，并且在实例化ChatCell之后给该变量赋值：
```
public var agora :AgoraRtcEngineKit!
```
之前说过播放远程用户的只需要实例化一个AgoraRtcVideoCancas之后再把uid赋值给它就可以了，而播放本地视频也是类似的方法，还需要区分一个用户是自己还是远程用户，因此需要传入当前用户的uid，在ChatCell中定义方法用来播放视频:
```
 func setUid(uid:UInt,localUid:UInt){
        labelUser.text=String(uid)
        let videoCanvas = AgoraRtcVideoCanvas()
        videoCanvas.uid=uid
        videoCanvas.view=videoView
        videoCanvas.renderMode = .render_Fit
        if(uid != localUid){
            agora.setupRemoteVideo(videoCanvas)
        }else{
            agora.setupLocalVideo(videoCanvas)
        }
    }
```
这样在多人聊天的时候就能使用分屏的方式播放用户聊天视频了，如果想放大某一个用户的视频该怎么办呢？
## 放大显示
当用户点击某一个UICollectionViewCell的时候，希望对应的视频能够放大显示。因为一个视频的播放只能显示在一个view上面，所以必须在点击一个UICollectionViewCell的时候把它的播放显示移除掉，在放大区域播放该聊天视频，为了预留足够空间显示放大的时候，还需要调整UICollextionViewCell的高度，给放大显示预留出足够的空间。
因此首先我们在`MutilChatViewController`中新增一个显示放大区域的view：
```
@IBOutlet weak var remoteView: UIView!
```
在放大视频的时候，不知道用户是需要放大自己的视频，还是放大远程用户的视频，因此首先要记录下用户自己的uid，在点击的时候拿到用户的uid，再判断是显示本地视频还是远程用户的视频，放大视频方法：
```swift
 func setupVideo(uid:UInt){
        if(self.remoteView.isHidden){
            self.remoteView.isHidden=false
        }
        let videoCanvas=AgoraRtcVideoCanvas()
        videoCanvas.uid=uid
        videoCanvas.view=remoteView
        videoCanvas.renderMode = .render_Fit
        if(uid==localUid){
            agoraKit.setupLocalVideo(videoCanvas)
        }else{
            agoraKit.setupRemoteVideo(videoCanvas)
        }
    }
```
当用户触发点击事件的时候使用变量`isSelect`记录用户的点击行为，如果用户有点击行为的时候，在判断Cell高度的时候就返回和Cell宽度一样的值（这里只是在demo的情况做的考虑，如果实际使用中还要根据Cell的个数进行显示高度的考虑）,判断高度方法：
```
func waterflowLayout(_ waterflowLayout: LDWaterflowLayout!, heightForItemAt index: UInt, itemWidth: CGFloat) -> CGFloat {
        let count:Int=(dataArray?.count)!
        if(self.isSelect!){
            return itemWidth
        }
        ....
    }
```
这样就可以在用户点击某一个Cell的时候进行放大显示的处理，在[上一篇文章](http://www.jianshu.com/p/baacf2c5ad6f)中，介绍了使用`reportAudioVolumeIndicationOfSpeakers `监听是谁在说话，如果真实的项目中，在每一个Cell中可以做一个小广播，在某一个用户说话的时候，可以通过小广播的变化进行标识。
这里使用Agora SDK做了一个简短的demo，后续的还会继续完善，利用Agora SDK模仿`Housparty`的功能实现比较简单，先要产品化还有很多的东西要做，在这里先做一个简单的总结吧！
## Agora SDK使用总结
Agora提供了高质量的视频通信SDK，覆盖了主流的操作系统，集成效率也比较高，而且还支持多个模式的视频通话，包括聊天，会议，直播等功能。SDK中API设计基本能够满足大部分的开发需要，而且隐藏了底层开发，这样对于应用层的开发者来说十分友好。非常适合有视频聊天开发需求的开发者。在视频领域创业大爆发的今天，建议更多的想要从事该领域的开发者可以尝试下。
在使用Agora iOS SDK的过程中有两个建议希望厂商可以考虑下:
1. 支持 Cocoapods，不支持Cocoapods需要在集成的时候在依赖上面花费时间，而且以后升级也不是太容易。
2. 希望可以提供基础UI的SDK，就像友盟的分享SDK一样，提供一套可用的带UI的SDK，当然还要允许用户定制，这样应用层的开发者集成效率会更高。

参考文档:[https://docs.agora.io/cn/user_guide/API/ios_api.html](https://docs.agora.io/cn/user_guide/API/ios_api.html)
demo地址:[https://github.com/jjz/agora-swift](https://github.com/jjz/agora-swift)

