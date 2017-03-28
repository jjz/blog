#react-native:环境搭建
```
Any application that than can be written in JavaScript will eventually be written in JavaScript -Jeff Atwood
```
React Native是Facebook开源的，在Javascript和React的基础上构建原生的Android和IOS应用的平台。
React Native着力于提高多平台的开发效率，`Learn once,write anywhere`。

下面简单介绍下React Native的环境搭建。

## 环境要求

最新的OS X系统 ，要开发IOS 应用就必须使用Mac OS系统。
Homebrew：安装教程 http://brew.sh/ ，Mac上的包管理软件
Node.js : https://nodejs.org/en/ 
watchman:https://facebook.github.io/watchman/docs/install.html 
facebook的开源的一个文件监视系统。
flow:http://flowtype.org/ facebook开源的一个JavaScript静态检查工具
## 安装
安装Homebrew:
>/usr/bin/ruby -e "$(curl -fsSLhttps://raw.githubusercontent.com/Homebrew/install/master/install)"

使用Homebrew安装node.js：
>brew install node 

安装watchman：
>brew install watchman

安装flow ：
>brew install flow

## 更新
如果已经安转了以上的软件，需要更新到当前最新版本。
首先更新Homebrew的版本库：
>brew update

更新Homebrew库的内容：
>brew upgrade

清除不再使用的资源:
>brew cleanup

## 安装react-native

npm是nodejs的包管理，使用npm可以安装nodejs的包，react-native也是nodejs的一个包。
使用npm安装react-native:
>npm install -g react-native

安装react-native命令行工具:
>npm install -g react-native-cli

运行命令:
>react-native -v

得到结果:
```
react-native-cli: 1.0.0
react-native: 0.20.0
```
react-native安装成功

## 第一个react-native项目
新建第一个react-native项目
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
运行ios:
>react-native run-ios

在当前目录下面多了一个ios的目录，里面新建一个project，HelloRN的项目。

运行Android
>react-native run-android

在当前目录下会多出一个anroid的目录，里面是android项目的文件。
运行`react-native`项目的时候首先会使用`react-native start`启动一个端口为**8081**的服务，用来提供JavaScript代码。

