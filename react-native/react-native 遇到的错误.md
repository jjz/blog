#react-native 遇到的错误
记录下在react-native开发中遇到的错误

## ReferenceError: Can't find variable: __fbBatchedBridge...
react-native启动用来做JavaScript的代码的服务，是本地的服务，App默认访问的host地址是`localhost`,运行到真实的设备上面的时候无法访问react-native服务,因此会出现上面的错误。
## ## #Android解决方案:
1. 对adb的server设置反向代理
>adb reverse tcp:8081 tcp:8081

2. 更改App内的服务地址
 * 摇晃手机
 * 点击菜单键
 * 在电脑上面运行命令
   >adb shell input keyevent 82

在`dev setting`里面即可设置。
   
## ## # ios解决方案:
 在`AppDelegate.m`中修改`jsCodeLocation`：
 ```
 .....
 jsCodeLocation = [NSURL URLWithString:@"http://192.168.31.191:8081/index.ios.bundle?platform=ios&dev=true"];
 ....
 ```

 

## react-native start 错误
错误信息：
```
ERROR: Unknown option --no-pretty

ERROR: Unknown option --no-pretty

Watchman:  watchman--no-pretty get-sockname returned with exit code 1 ERROR: Unknown option --no-pretty

 ERROR  watchman--no-pretty get-sockname returned with exit code 1 ERROR: Unknown option --no-pretty

Error: watchman--no-pretty get-sockname returned with exit code 1 ERROR: Unknown option --no-pretty

    at ChildProcess.<anonymous> (.../fb-watchman/index.js:198:18)
    at emitTwo (events.js:106:13)
    at ChildProcess.emit (events.js:191:7)
    at maybeClose (internal/child_process.js:852:16)
    at Socket.<anonymous> (internal/child_process.js:323:11)
    at emitOne (events.js:96:13)
    at Socket.emit (events.js:188:7)
    at Pipe._handle.close [as _onclose] (net.js:492:12)
```
解决方法：
`npm`下面的包也有watchman,如果安装了先卸载
>npm r -g watchman 
重新安装`watchman`:
```
brew uninstall watchman 
brew link pcre 
brew install --HEAD watchman
```
如果出现下面的提示错误:
```
Error: The `brew link` step did not complete successfully
brew link --overwrite watchman
......
```
直接运行命令:
>brew link --overwrite watchman
出现*Linking*创建的信息:
```
Linking /usr/local/Cellar/watchman/HEAD-bbd5957... 4 symlinks created
```

如果出现权限不足的情况,可以运行命令:
>sudo chown -R `whoami` /usr/local


