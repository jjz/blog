# 自定义cocoapods库

做Ios开发会使用`Cocoapods`来管理第三方依赖库，非常好用，指定依赖，自动下载依赖。
关于`Cocoapods`的安装可以参考[CocoaPods安装和使用教程](http://code4app.com/article/cocoapods-install-usage)这篇文章。

有一种场景：有2个项目，需要提取一个出一个公共的库提供给两个项目使用，怎么使用`Cocoapods`解决呢？
可以自定义一个`Cocoapods`的库来解决这个问题，不需要提交到`Cocoapods`。
自定义一个公共`Cocoapods`库可以封装公共代码，提高代码的复用，减少代码的维护。
自定义`Cocoapods`库，需要先生成`podspec`。
##  生成podspec
**podspec**是用来指定为`Cocoapods类库`的配置文件(Ruby语法)
可以使用命令创建`podspec`文件：
> pod lib create MyLibrary

也可以自己使用编辑器编写。
一般`podspec`的内容都是下面这样的:
```
    Pod::Spec.new do |s|
      s.name             = "pod-library"
      s.version          = "0.1.0"
      s.license          = 'Apache License, Version 2.0'
      s.summary          = "A short description of pod-library."
      s.description      = <<-DESC
                           An optional longer description of pod-library
    
                           * Markdown format.
                           * Don't worry about the indent, we strip it!
                           DESC
      s.homepage         = "https://github.com/jjz/pod-library"
    
      s.author           = { "jjz" => "woaf1003@gmail.com" }
      s.source           = { :git => "https://github.com/jjz/pod-library.git", :tag => s.version.to_s }
      s.social_media_url = 'https://github.com/jjz'
    
      s.platform     = :ios, '7.0'
      s.requires_arc = true
    
      s.source_files = 'pod-library/*.{h,m}'
      s.resources = 'pod-library/images.xcassets/**/*.png'
      s.dependency 'AFNetworking', '~> 2.0'
      s.vendored_frameworks='**/*.framework'
      s.preserve_paths = 'LibraryName.a'
      s.frameworks = 'QuartzCore'
    end
```
这段代码的意思是:创建一个一个`Spec`的配置,其中：

 *  `source_files` 指定类库中的源码
需要在`source_files`中指定要发布的源代码文件，这个项目的源代码都是在**pod-library**目录下面，因此我指定了获取**pod-library**目录下的所有的`.h`和`.m`文件
如果你需要使用`swift`就需要指定目录下面的.swift文件,如果有`c++`源代码的还需要指定`.mm`文件。
多个目录使用`,`分割类似于这样：
>'pod-library/*.{h,m}’，'pod-library/utils/*.{h,m}’

 * dependency指定类库中的依赖库
这个语法和podfile文件里面的pod文件很像，`s.dependency`后面指定类库名称和版本，如果需要在加类库的话再新起一行,比如：
 > s.dependency 'OpenSSL', '1.0.110'

 * 指定类库中的资源文件
指定类库中的资源文件，我们可以抽象出来一些公用的控件，比如*progress*，*alert*,*prompt* 等，这些控件需要使用一些资源文件。
有两种指定的方式：
> resources 和 resource_bundles
`resources` 指定的资源在调用类库的项目中可以直接调用，和调用项目内的资源一样。
`resource_bundles` 把需要调用的资源编译到bundle来调用，
在这里我使用的是`resources`。

一般我们有可能打包的资源有` 声音，图片，xib`...

* `name`      类库的名称
* `version`   指定版本
* `license`   开源协议
* `description`  简单描述
* `homepage`   个人主页
* `author` 作者信息
* `source`  指定git地址
* `platform` 指定iOS版本
* `requires_arc`  指定支持arc
*  `vendored_frameworks` 本地的frameworks
*  `frameworks` 指定需要使用的系统frameworks
*  `preserve_paths` 本地的`.a`库



## 在项目中调用pod-library
创建一个和`pod-library`同级的目录，命名为pod-project,用来调用`pod-library`。
在pod-project目录中添加Podfile。
指定对pod-library的依赖：
```
    platform :ios, '7.0'
    
    pod ‘pod-library’, :path => ‘../pod-library’
```
这里需要使用path来指定本地的目录作为库的地址
运行安装命令：

> pod install
```
    pdating local specs repositories
    Analyzing dependencies
    Fetching podspec for `pod-library` from `../pod-library`
    Downloading dependencies
    Installing AFNetworking (2.5.4)
    Installing pod-library (0.1.0)
    Generating Pods project
    Integrating client project
```

可以看到pod先去下载pod-library，然后再下载已经指定的pod-library的依赖。
 

## 在pod-project更改pod-library的代码

下载完依赖库之后，打开`pod-project.xcworkspace`文件，会看到pods多了一个`development pods`的目录
这个目录下有`pod-library`项目的代码以及资源。
可以在这里直接编辑`pod-library`里的代码，在`pod-library`中提交代码。这样就可以在主项目中编辑代码，在类库项目中提交了。

参考 :
[making cocoa pods](https://guides.cocoapods.org/making/index.html)
  [Using Pod Lib Create](https://guides.cocoapods.org/making/using-pod-lib-create.html)

开源地址：
[pod-library](https://github.com/jjz/pod-library)
[pod-project](https://github.com/jjz/pod-project)


