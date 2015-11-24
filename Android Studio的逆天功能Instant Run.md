# Android Studio2.0的逆天功能Instant Run
作为一个Android开发者，很多的时候我们需要花大量的时间在bulid，运行到真机（虚拟机）上，对于ios上的`Playground`羡慕不已，这种情况将在Android Studio 2.0有了很大改善，使用`instant run`，在第一次运行之后，就可以快速的在真机中看见修改后的结果，不仅仅是UI可以直接显示，还包括代码逻辑。不用再苦苦等build了，节约生命呀！
##首先要升级到Android Studio 2.0
目前Android Studio的2.0版本还在`Canary Channel`（金丝雀） 上面，所以想体验2.0的同学需要先把升级版本切换到Canary Channel 上面。

 >Preferences -> System Settings ->Updates

![图片描述][1]

可以切换升级版本

然后`check for updates` 就可以升级了。（如果连接不上升级服务器，请墙一下）

## 升级android tools build
`instant run`功能之后再`android tools build` 的`2.0.0`的版本才可以使用。
需要在`build.gradle`中指定

```
buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:2.0.0-alpha1'
    }
}
```
##设置instant Run
>Preferences -> Build,Execution,Deployment -> Instant Run

![图片描述][2]
中有关于 Instant Run的设置

*第一个设置是,是否开启Instant Run的，默认是开启。*

*第二个是,当代码变动的时候重启activity（亲测没有效果，不知道是不是还不是太完善）*

*第三个是，每次变动的时候都有个toast提示下（我选择关闭了）*

##运行Instant Run 
再没有运行项目的时候，我们的Run图标和以前是一样的。
![图片描述][3]
(话说这个Debug的图标好可爱)

运行了项目之后的图标是这样的：

![图片描述][4]

运行之后的Dialog框是这样的，和debug的时候一样多了个Stop的按钮

![图片描述][5]

再改完代码之后直接点击Run按钮就可以`立刻`就能在你的设备上面看到效果。

目前还是有一些小的问题,偶尔还是会出现build ->run到设备的方式，期待Google尽快的把instant Run做的更加完善，也期望AS2.0的正式版本。
另外google说优化了虚拟机部分，性能提高了`50`倍，是不是可以抛弃Genymotion了？ 


  [1]: /img/bVq642
  [2]: /img/bVq644
  [3]: /img/bVq65a
  [4]: /img/bVq65b
  [5]: /img/bVq65c



