#React Native ios离线包
在开发React Native的过程成,js代码和图片资源运行在一个Debug Server上面，每次更新代码之后只需要使用`command+R`键刷新就可以看到代码的更新。
当我们需要发布App到App Store的时候就需要打包离线的js代码和图片到XCode项目中。这样就需要打包离线资源，在XCode中添加离线资源。再发布到App Strore中。
打包离线资源需要使用命令`react-native bundle`
##react-native bundle
使用React Native开发的ios应用可以使用` react-native bundle`命令进行打包，`react-native bundle`的详细命令选项在[https://github.com/facebook/react-native/blob/master/local-cli/bundle/bundleCommandLineArgs.js](https://github.com/facebook/react-native/blob/master/local-cli/bundle/bundleCommandLineArgs.js)。
其中我们常使用的命令选项：

* --entry-file ios或者android入口的js名称，比如**index.ios.js**
* --platform 平台名称,ios或者android
* --dev 设置为false的时候将会对JavaScript代码进行优化处理
* --bundle-output 生成的JavaScript的名称，比如`./ios/bundle/index.ios/jsbundle`
* --assets-dest 图片以及其他资源存在的地址,比如`./ios/bundle`

打包命令如下:
> react-native bundle --entry-file index.ios.js  --platform ios --dev false --bundle-output ./ios/bundle/index.ios.jsbundle --assets-dest ./ios/bundle

为了方便使用，可以把打包命令写到npm script中:
```
"scripts": {
    "start": "node node_modules/react-native/local-cli/cli.js start",
    "bundle-ios":"node node_modules/react-native/local-cli/cli.js bundle --entry-file index.ios.js  --platform ios --dev false --bundle-output ./ios/bundle/index.ios.jsbundle --assets-dest ./ios/bundle"

  },
```
然后运行命令:
>npm run bundle-ios
打包结果:
```
...
transformed 360/360 (100%)
[8:56:31 PM] <END>   find dependencies (3427ms)
bundle: start
bundle: finish
bundle: Writing bundle output to: ./ios/bundle/index.ios.jsbundle
bundle: Done writing bundle output
bundle: Copying 5 asset files
bundle: Done copying assets
```


##添加资源
离线包生成完成之后，可以在*ios*目录下看到**bundle**目录，这个目录就是离线包生成的资源。
需要在Xcode中添加资源到项目中，必须使用**Create folder references**的方式添加文件夹.
1. Add Files to "RNIos"
2. 选择`bundle`文件,在*option*中选择**Create folder references**
添加到项目中的文件夹必须是蓝色:

##jsCodeLocation

在React Native中`AppDelegate`中可以看到设置JavaScript代码位置的代码：

```
NSURL *jsCodeLocation;
  jsCodeLocation = [NSURL URLWithString:@"http://localhost:8081/index.ios.bundle?platform=ios&dev=true"];

  RCTRootView *rootView = [[RCTRootView alloc] initWithBundleURL:jsCodeLocation
                                                      moduleName:@"RNIos"
                                               initialProperties:nil
                                                   launchOptions:launchOptions];
```
在开发的过程中可以在这里配置Debug Server的地址，当发布上线的时候，就需要还用发布的jsbundle文件，因为需要设置jsCodeLocation为本地的离线jsbundle。
```
jsCodeLocation = [[NSBundle mainBundle] URLForResource:@"bundle/index.ios" withExtension:@"jsbundle"];
```
离线包里的.jsbundle文件是经过优化处理的，因此运行效率也会比Debug的时候更高一些。

项目代码地址:[https://github.com/jjz/react-native/tree/master/RNIos](https://github.com/jjz/react-native/tree/master/RNIos)


