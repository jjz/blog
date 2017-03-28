#React Native ios打包
开发React Native的过程成,js代码和图片资源运行在一个`Debug Server`上，每次更新代码之后只需要使用`command+R`键刷新就可以看到代码的更改，这种方式对于调试来说是非常方便的。
但当我们需要发布App到`App Store`的时候就需要打包,使用离线的js代码和图片。这就需要把JavaScript和图片等资源打包成离线资源，在添加到Xcode中，然后一起发布到`App Strore`中。
打包离线资源需要使用命令`react-native bundle`(注：文中使用的项目名称为`RNIos`)
## react-native bundle
React Native的` react-native bundle`命令是用来进行打包的命令，`react-native bundle`的详细命令选项[https://github.com/facebook/react-native/blob/master/local-cli/bundle/bundleCommandLineArgs.js](https://github.com/facebook/react-native/blob/master/local-cli/bundle/bundleCommandLineArgs.js)。
其中我们常使用的一线命令选项：

* --entry-file ,ios或者android入口的js名称，比如**index.ios.js**
* --platform ,平台名称(ios或者android)
* --dev ,设置为false的时候将会对JavaScript代码进行优化处理。
* --bundle-output, 生成的jsbundle文件的名称，比如`./ios/bundle/index.ios.jsbundle`
* --assets-dest 图片以及其他资源存放的目录,比如`./ios/bundle`

打包命令如下:
> react-native bundle --entry-file index.ios.js  --platform ios --dev false --bundle-output ./ios/bundle/index.ios.jsbundle --assets-dest ./ios/bundle

为了方便使用，也可以把打包命令写到npm script中:
```
"scripts": {
    "start": "node node_modules/react-native/local-cli/cli.js start",
    "bundle-ios":"node node_modules/react-native/local-cli/cli.js bundle --entry-file index.ios.js  --platform ios --dev false --bundle-output ./ios/bundle/index.ios.jsbundle --assets-dest ./ios/bundle"

  },
```
然后运行命令直接打包:
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
可以看到jsbundle和资源文件已经打包成功。


## 添加资源
离线包生成完成之后，可以在*ios*目录下看到一个**bundle**目录，这个目录就是`bundle`生成的离线资源。
需要在Xcode中添加资源到项目中，必须使用**Create folder references**的方式添加文件夹.
1.  Add Files to "RNIos"
![add Files](http://upload-images.jianshu.io/upload_images/22188-f509f0b987ef785d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
2.  选择`bundle`文件,在*option*中选择**Create folder references**
![选择文件夹](http://upload-images.jianshu.io/upload_images/22188-954ddb1a328133ec.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
3. 添加到项目中的文件夹必须是蓝色
![文件夹必须是蓝色](http://upload-images.jianshu.io/upload_images/22188-0ed8656c145cd170.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## jsCodeLocation
在ios中`AppDelegate`里可以看到设置JavaScript代码位置的代码：
Debug Server上的设置
```
NSURL *jsCodeLocation;
  jsCodeLocation = [NSURL URLWithString:@"http://localhost:8081/index.ios.bundle?platform=ios&dev=true"];

  RCTRootView *rootView = [[RCTRootView alloc] initWithBundleURL:jsCodeLocation
                                                      moduleName:@"RNIos"
                                               initialProperties:nil
                                                   launchOptions:launchOptions];
```
在开发的过程中可以在这里配置Debug Server的地址，当发布上线的时候，就需要使用离线的jsbundle文件，因此需要设置jsCodeLocation为本地的离线jsbundle。
设置离线的jsCodeLocation:
```
jsCodeLocation = [[NSBundle mainBundle] URLForResource:@"bundle/index.ios" withExtension:@"jsbundle"];
```
离线包里的.jsbundle文件是经过优化处理的，因此运行效率也会比Debug的时候更高一些。

项目代码地址:[https://github.com/jjz/react-native/tree/master/RNIos](https://github.com/jjz/react-native/tree/master/RNIos)


