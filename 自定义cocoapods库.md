经常使用cocoapods来管理第三方依赖库，非常好用

关于cocoapods的安装可以参考CocoaPods安装和使用教程

http://code4app.com/article/cocoapods-install-usage这篇文章。

现在我遇到了一个问题：我有2个项目，需要提取一个出一个公共的库，怎么使用cocoapods解决这个问题呢？

这里就需要自定义一个cocoapods的开发库，来帮助封装公共代码。

参考 :
[making cocoa pods](https://guides.cocoapods.org/making/index.html)
	 [Using Pod Lib Create](https://guides.cocoapods.org/making/using-pod-lib-create.html)

 

1. 生成podspec
------------

*podspec是用来指定为cocoapods类库的配置文件*
可以用

    

> pod lib create MyLibrary

命令来生成
也可以自己编写
一般podspec的内容都是这样的:

    Pod::Spec.new do |s|
      s.name             = "pod-library"
      s.version          = "0.1.0"
      s.license          = 'Apache License, Version 2.0'
      s.summary          = "A short description of pod-library."
      s.description      = <<-DESC
                           An optional longer description of pod-library
    
                           * Markdown format.
                           * Don't worry about the indent, we strip it!
                           DESC
      s.homepage         = "https://github.com/jjz/pod-library"
    
      s.author           = { "jjz" => "woaf1003@gmail.com" }
      s.source           = { :git => "https://github.com/jjz/pod-library.git", :tag => s.version.to_s }
      s.social_media_url = 'https://github.com/jjz'
    
      s.platform     = :ios, '7.0'
      s.requires_arc = true
    
      s.source_files = 'pod-library/*.{h,m}'
      s.resources = 'pod-library/images.xcassets/**/*.png'
      s.dependency 'AFNetworking', '~> 2.0'
    end

这句话的意思就是创建一个Spec的配置
`name`      类库的名称
`version`   指定版本
`license`   开源协议
`description`  简单描述
`homepage`   个人主页
`author` 作者信息
`source`  指定git地址
`platform` 指定iOS版本
`requires_arc`  arc

*后面的几个参数下面重点介绍下*

 - source_files 指定类库中的源码

需要在source_files中指定要发布的源代码文件，这个项目的源代码都是在pod-library目录下面，因此我指定了获取pod-library目录下得所有的.h和.m文件
如果你需要使用swift就需要指定 目录下面的.swift文件,如果有c++源代码的还需要指定.mm文件的目录。多个目录使用,分割类似于这样：

    'pod-library/*.{h,m}’，'pod-library/utils/*.{h,m}’

 - dependency指定类库中的依赖库
这个语法和podfile文件里面的pod文件很想，s.dependency后面指定类库名称和版本，如果需要在加类库的话再新起一行 
比如：

 s.dependency 'OpenSSL', '1.0.110'
 - 指定类库中的资源文件
指定类库中的资源文件，我们可以抽象出来一些公用的控件，比如progress，alert,prompt 等，
有两种指定的方式

> resources 和 resource_bundles

`resources` 中指定的资源调用类库的项目可以直接调用，和调用项目内的资源一样
`resource_bundles` 是把需要调用的资源编译到bundle来调用，
在这里我使用的是`resources`

一般我们有可能打包的资源 声音，图片，xib...

2. 在项目中调用pod-library
--------------------

建立一个和pod-library同级的目录，命名为pod-project
在pod-project目录中Podfile
指定对pod-library的依赖

    platform :ios, '7.0'
    
    pod ‘pod-library’, :path => ‘../pod-library’

这里需要使用的是path来指定本地的目录
然后 运行

> pod install

    pdating local specs repositories
    Analyzing dependencies
    Fetching podspec for `pod-library` from `../pod-library`
    Downloading dependencies
    Installing AFNetworking (2.5.4)
    Installing pod-library (0.1.0)
    Generating Pods project
    Integrating client project

会看到pod先去下载pod-library的依赖，然后下载指定pod-library的依赖
 

3. 在pod-project更改pod-library的代码
-------------------------------

下载完依赖库之后，打开`pod-project.xcworkspace`文件，会看到pods多了一个`development pods`的目录
这个目录下有`pod-library`项目的代码以及资源。
可以在这里编辑`pod-library`里的代码，在`pod-library`中提交。

开源地址：
[pod-library](https://github.com/jjz/pod-library)
[pod-project](https://github.com/jjz/pod-project)

