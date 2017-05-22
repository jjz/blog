# 自定义cocoapods库

做iOS开发时常常会使用`Cocoapods`来管理第三方依赖库，指定好依赖之后，自动下载依赖即可。
关于`Cocoapods`的安装可以参考这篇文章[CocoaPods安装和使用教程](http://code4app.com/article/cocoapods-install-usage)。
现在有一个场景：有2个项目，需要提取一个出一个公共的库提供给两个项目使用，如何使用`Cocoapods`解决使用公共库的问题呢？
通过自定义一个`Cocoapods`的库可以解决这个问题，自定义一个公共`Cocoapods`库的好处：可以封装公共代码，提高代码的复用，减少代码的维护。
自定义`Cocoapods`库，可以通过**pod**命令进行创建。
## 创建Cocoapods库
**podspec**是`Cocoapods类库`的配置文件(Ruby语法)
可以使用命令行创建Cocoapods类库，命令如下：
> pod lib create MyLibrary

运行完命令之后需要选择语言已经测试代码等，按照自己的项目需求选择即可：
```
To get you started we need to ask a few questions, this should only take a minute.

If this is your first time we recommend running through with the guide:
 - http://guides.cocoapods.org/making/using-pod-lib-create.html
 ( hold cmd and click links to open in a browser. )
What language do you want to use?? [ Swift / ObjC ]
 > Swift
Would you like to include a demo application with your library? [ Yes / No ]
 > Yes
Which testing frameworks will you use? [ Quick / None ]
 > Quick
Would you like to do view based testing? [ Yes / No ]
 > Yes
Running pod install on your new library.
```
安装完依赖之后，会得到一个`pod-library.podspec`文件，他的内容一般都是下面这样的:
```
    Pod::Spec.new do |s|
  s.name             = 'pod-library'
  s.version          = '0.1.0'
  s.summary          = 'A short description of pod-library.'
  ...
  s.description      = <<-DESC
TODO: Add long description of the pod here.
                       DESC

  s.homepage         = 'https://github.com/jjz/pod-library'
  # s.screenshots     = 'www.example.com/screenshots_1', 'www.example.com/screenshots_2'
  s.license          = { :type => 'MIT', :file => 'LICENSE' }
  s.author           = { 'jjz' => 'woaf1003@gmail.com' }
  s.source           = { :git => 'https://github.com/jjz/pod-library.git', :tag => s.version.to_s }
  s.ios.deployment_target = '8.0'
  s.source_files = 'pod-library/Classes/**/*'
 ...
end

```
这段Ruby代码的意思是创建一个`Spec`的配置，该项目发布之后可以被Pod直接引用，其中一些选项的意义如下：
 *  `source_files` 指定类库中的源码
在`source_files`中指定要发布的源代码文件，这个项目的源代码都是在**pod-library**(项目)目录下，在这里指定了获取`pod-library/Classes/**/* `目录下的所有文件。
也可以分别指定不同的文件，比如需要使用`swift`就需要指定目录下面的.swift文件，有`c++`源代码的还可以指定`.mm`文件。
多个目录使用`,`分割类似于下面的配置：
>'pod-library/*.{h,m}’，'pod-library/utils/*.{h,m}’

 * dependency指定类库中的依赖库
这个语法和podfile文件里面的pod文件很像，`s.dependency`后面指定类库名称和版本，如果加其他类库的话需要再新起一行，比如添加OpenSSL的类库：
 > s.dependency 'OpenSSL', '1.0.110'

 * 指定类库中的资源文件
指定类库中的资源文件，我们可以抽象出来一些公用的控件，比如*progress*，*alert*，*prompt* 等，这些控件需要使用资源文件。一般我们有可能打包的资源有` 声音，图片，xib`...，有两种指定资源文件的方式：
> resources 和 resource_bundles

`resources` 指定的资源在调用类库的项目中可以直接调用，和调用项目内的资源一样。
而`resource_bundles`把需要调用的资源编译到bundle来调用，
在这里我使用的是`resources`定义资源文件。
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

使用上面的配置可以完成把一个项目配置成Cocoapods类库，在项目中有些只是配置成一个类库并不能满足需求，有可能会需要一个以上的类库，例如一个是`core`核心类库，一个是`logic`业务逻辑类库，还可以使用`subspec`实现上述目的。
## subspec
`subspec`是pod的一个属性，用来指定pod类库中的子类库。配置subspec首先需要指定一个子类库，之后再指定子类库对于的配置即可。配置如下：
```
s.subspec "Core" do |core|
    core.source_files="pod-library/Core/**/*"
  end
```
在定义一个自类库，该类库依赖上面定义的子类库`Core`，定义如下：
```
s.subspec "Logic" do |logic|
    logic.dependency 'pod-library/Core'
    logic.source_files="pod-library/Logic/**/*"
  end
```
这样就完成了两个子类库的定义，在调用的时候直接指定对应的类库即可，例如:
> pod 'pod-library/Core', :path => '../'

以上是定义类库的内容，下面再看下如何在其他项目中调用已定义的类库。
## 在项目中调用pod-library
创建一个和`pod-library`同级的目录，命名为pod-project,用来调用`pod-library`。
在pod-project目录中添加Podfile。
指定对pod-library的依赖：
```
  platform :ios, '8.0'
  target "pod-project" do
      pod 'pod-library', :path => 'pod-library'
      use_frameworks! 
  end
```
注意，这里必须使用` use_frameworks!`，Pod中使用Swift的类库必须使用其标识，不然会出现错误:
```
Pods written in Swift can only be integrated as frameworks; this feature is still in beta. Add `use_frameworks!` to your Podfile or target to opt into using it.
```
这里需要使用了path来指定本地的目录作为库的地址，这样的作用是可以在`pod-project`项目中直接更新`pod-library`中的代码,运行安装命令：

> pod install


可以看到pod先去下载pod-library，然后再下载pod-library的依赖。
 在pod-project如何更改pod-library的代码呢？
下载完依赖库之后，打开`pod-project.xcworkspace`文件，会看到pods多了一个`development pods`的目录，这个目录下有`pod-library`项目的代码以及资源。可以在这里直接编辑`pod-library`里的代码，然后就可以直接提交代码了，这是和其他的pods里的文件最大的区别，不加path的文件修改之后的文件在运行`pod install`或`pod update`之后就会被重新覆盖掉。
（这里pod-library使用的是`git submodule`的形式包含在项目中的）

参考 :
[making cocoa pods](https://guides.cocoapods.org/making/index.html)
  [Using Pod Lib Create](https://guides.cocoapods.org/making/using-pod-lib-create.html)

开源地址：
[pod-library](https://github.com/jjz/pod-library)
[pod-project](https://github.com/jjz/pod-project)

