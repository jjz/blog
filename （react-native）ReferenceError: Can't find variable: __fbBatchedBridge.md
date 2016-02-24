# （react-native）ReferenceError: Can't find variable: __fbBatchedBridge

把react-native运行Android设备上的时候出现错误：
```
ReferenceError: Can't find variable: __fbBatchedBridge...
```
解决方案：
确保你的电脑和手机处于同一个wifi网络，在手机启动你的app。
进入菜单页面：
1.摇晃手机
2.点击菜单键
3.在电脑上面运行命令
>adb shell input keyevent 82

#设置 Debug server host & port
设置为`<电脑ip>:8081`
返回主页面，选择`Reload JS`
页面显示正常



