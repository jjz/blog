# 是时候考虑下该如何一个好的Splash页了?
第一次写产品相关的文章，首先从启动开始吧！启动App都会使用一个页面:`Splash页`也叫做开屏页，这篇文章就聊下`Splash页`吧！
## 什么是Splash页
`Splash页`的定义：
>当应用程序(app)启动的时候，启动是要花费一定的时间的，这个时候往往在程序启动过程中会使用一些过渡页面（或动画），这些被成为启动页。由于启动页在每次打开应用时都会出现，并且往往停留很短的时间，就像闪现的效果一样，所以启动页也常常被成为闪屏或者Splash页。

为什么我们需要一个启动页，在App打开的一刻，系统需要加载应用程序的资源，还有可能加载一些缓存资源，可能还有网络请求，这些处理都需要花费一定的时间。如果这段时间没有给用户反馈和展现，用户会以为程序出了问题或者由于等待时间过长没有得到预期的结果而心情烦躁。而这个启动时间客观存在的，所以就有人提出了不如利用启动页做些事情,比如：
>传递信息（商业信息、产品的核心作用）、故事或是产品的人文情怀

有人就应用启动页还总结了:[应用黄金七秒启动页](http://www.leiphone.com/news/201406/1023-warlial-sevensec.html)里面有一个关于启动页控制在多少时间的观点：
>启动页在时间上最好控制在3秒以下，超过3秒用户就会有焦急感，如果启动页又没有加载过程等状态反馈，用户很容易就直接退出应用。

所以有些应用会在`Splash页`上停留一段时间，让用户更多的注意力在启动页上面，以达到:
1. 品牌宣传
2. 情感共鸣
3. 情景故事演绎

那当前`Splash页`的现状又是如何了？
## Splash现状
微信的经典页面：
![微信](http://upload-images.jianshu.io/upload_images/22188-0cd86519b530ecec.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/540)
微博的：
![微博](http://upload-images.jianshu.io/upload_images/22188-d3661d6ecf485c5f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/540)
唯美的：

![唯美](http://upload-images.jianshu.io/upload_images/22188-efaac5bf4bb4efd4.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/540)

情怀的：

![情怀](http://upload-images.jianshu.io/upload_images/22188-22e7f0bbb44353ea.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/540)

可交互的：
![多张](http://upload-images.jianshu.io/upload_images/22188-6be88a5fa0ea6b42.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/540)
每次都启动，切`Splash页`带动画的：
![滴滴](http://upload-images.jianshu.io/upload_images/22188-f2d6148cbdbc0239.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/540)

每次启动之后，播放完一页之后还有一页的：

![ofo1](http://upload-images.jianshu.io/upload_images/22188-6d79dca722f29fd3.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/340)

![ofo2](http://upload-images.jianshu.io/upload_images/22188-2050945b21691cb6.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/340)



## 什么样的Splash页才是好的
那什么样的`Splash`才是好的启动页呢？
罗振宇在2017年在他的跨年演讲中说了一个时间战场的观点:
>未来，在时间这个战场上，有两门生意会特别值钱，第一，就是帮别人省时间。第二，就是帮别人把省下来的时间浪费在那些美好的事物上。

张小龙在微信公开课上阐述了"用完即走"的理念:
>任何产品都只是一个工具，对工具来说，好的工具就是应该最高效率的完成任务，然后尽快离开。

他们都表述了一个事实，用户的时间很有限，有限的时间要花在有用的事情上面，有限的时间要花在美好的事情上面，浪费用户时间而又对于有没有价值的事情在产品里面尽量要少做。比如：`Splash页`。
好的Splash页应该是什么样的?这里还有苹果在「`iOS Human Interface Guidelines`」中关于启动的描述：
>为了增强应用程序启动时的用户体验，您应该提供一个启动图像。启动图像与应用程序的首屏幕看起来非常相似。当用户在主屏幕上点击您的应用程序图标时，iPhone OS会立即显示这个启动图像。一旦准备就绪，您的应用程序就会显示它的首屏幕，来替换掉这个启动占位图像。一定要强调的是，之所以提供启动图像，是为了改善用户体验，并不是为了提供：
「应用程序进入体验」，比如启动动画
「关于」窗口
品牌宣传元素，除非它们是您应用程序首屏幕的静态组成部分

苹果描述启动的这个章节也叫做`立即启动`，`iOS 程序应该在用户想用它们的时候立刻启动，毫无延迟 `,避免呈现"关于"或者"splash"页面，苹果的建议是使用一张和应用程序第一屏一样的启动图片，这样可以缩短对启动时间的感受。也就是说让用户意味应用已经启动，其实只是一张图而已，而只要启动时间足够短，当用户真正想做操作的时候，主页面已经加载完成了，而用户是感知不到启动时间的。
>好的`Splash页`就是没有`Splash页`

你需要优化你应用的启动时间，尽量让主页面的启动控制在200ms左右，这个时间用户很难感知到应用的启动,这样的好处是用户打开你的App就能有直接进入的感觉，得到自己想要的功能，而不是每次打开时都需要欣赏3秒情怀图片。
另外不能只看到微信经典图片的情怀，而不注意到，微信在进程不被杀死的情况下再次打开微信是不会出现`Splash页`(android back键返回不会杀死进程)。

用户的时间越来越重要，让用户把时间花费在有价值的事情上，减少用户的等待，用户打开App是想要功能(价值)，而不是每次都有的：3秒钟情怀。

