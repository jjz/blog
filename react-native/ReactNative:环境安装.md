# react-native:环境安装
环境要求：OS X ，要开发ios App就必须使用Mac OS系统。
Homebrew：安装教程 http://brew.sh/，Mac上的包管理软件
Node.js : https://nodejs.org/en/ 
watchman:https://facebook.github.io/watchman/docs/install.html,facebook的一个文件监视系统。
flow:http://flowtype.org/ js的变量检查

##安装
安装Homebrew:
>/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

安装node.js
>brew install node 

安装watchman
>brew install watchman

安装flow 
>brew install flow

##更新当前软件到最新版本
更新Homebrew版本库
>brew update

更新软件
>brew upgrade

##安装react-native
>npm install -g react-native-cli
```
─┬ react-native-cli@0.1.10
  ├─┬ chalk@1.1.1
  │ ├─┬ ansi-styles@2.2.0
  │ │ └── color-convert@1.0.0
  │ ├── escape-string-regexp@1.0.5
  │ └── strip-ansi@3.0.1
  ├─┬ prompt@0.2.14
  │ ├─┬ read@1.0.7
  │ │ └── mute-stream@0.0.6
  │ └─┬ utile@0.2.1
  │   ├── i@0.3.4
  │   └─┬ rimraf@2.5.2
  │     └─┬ glob@7.0.0
  │       ├─┬ minimatch@3.0.0
  │       │ └─┬ brace-expansion@1.1.3
  │       │   └── balanced-match@0.3.0
  │       └── once@1.3.3
  └── semver@5.1.0
```
react-native安装成功

##第一个react-native项目
>react-native init HelloRN 

```
o run your app on iOS:
   cd /Users/***/Documents/github/react-native/HelloRN
   react-native run-ios
   - or -
   Open /Users/***/Documents/github/react-native/HelloRN/ios/HelloRN.xcodeproj in Xcode
   Hit the Run button
To run your app on Android:
   Have an Android emulator running (quickest way to get started), or a device connected
   cd /Users/***/Documents/github/react-native/HelloRN
   react-native run-android
```
##运行
>react-native run-ios

在当前目录下面多了一个ios的目录，里面新建一个project，HelloRN的项目。

>react-native run-android

在当前目录下会多出一个anroid的目录，里面是android项目的文件。


