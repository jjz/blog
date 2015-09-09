##首先要安装Meteor 
[安装Meteor][1]

##在Meteor中安装Ios SDK
>meteor install-sdk ios

    ✓ Xcode is installed
	✓ Xcode license agreed

##对Meteor App添加对IOS的支持
>meteor add-platform ios
    ios: added platform
该项目现在已经支持IOS了

##在IOS上运行Meteor

>meteor run ios 

会安装`Cordova`插件

    Cordova提供了一组设备相关的API，通过这组API，移动应用能够以JavaScript访问原生的设备功能，如摄像头、麦克风等。
    
![ios虚拟机运行效果][1]

>meteor run ios-device
使用这个命令可以让APP在真机上面运行
在真机上面运行，首先Meteor会构建一个XCode项目，直接在这个中选择你要运行的机器即可
![XCode项目截图][3]
真机运行效果:
![真机运行效果截图][4]


  [1]: /img/bVpN7o
  [2]: /img/bVpN7o
  [3]: /img/bVpN7B
  [4]: /img/bVpN7C

