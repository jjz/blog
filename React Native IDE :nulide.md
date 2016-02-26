# React Native 的开发工具:Nulide
做React Native开发的时候,并没有一个比较好的IDE,Android Studio打开Android下的项目还可以,JS部分的代码打开并不方便,也没有一个比较好的方式运行React Natvie的服务,每次run的时候都需要启动一个React Native的服务。
还好有Nuclide,Nuclide是Facebook专门为React开发的IDE。Nuclide拥有更好的语法补全，类型检查等,Nulide是在Atom的基础上的一系列的插件集，Atom是Github打造的下一代编程开发利器，是一个完全用JavaScript开发的编辑器
Atom下载地址：https://atom.io/

##安装Nuclide
通过apm可以安装Atom的插件

>apm install nuclide
```
Installing nuclide to /Users/***/.atom/packages [BABEL] Note: The code generator has deoptimised the styling of "/Users/jjz/.atom/packages/nuclide/pkg/nuclide/debugger/atom/VendorLib/devtools/front_end/cm/codemirror.js" as it exceeds the max of "100KB".
[BABEL] Note: The code generator has deoptimised the styling of "/Users/***/.atom/packages/nuclide/pkg/nuclide/debugger/atom/VendorLib/devtools/front_end/elements/StylesSidebarPane.js" as it exceeds the max of "100KB".
[BABEL] Note: The code generator has deoptimised the styling of "/Users/***/.atom/packages/nuclide/pkg/nuclide/hack/VendorLib/hh_ide.js" as it exceeds the max of "100KB".
[BABEL] Note: The code generator has deoptimised the styling of "/Users/***/.atom/packages/nuclide/pkg/nuclide/react-native-inspector/VendorLib/dev-tools/standalone.js" as it exceeds the max of "100KB".
✓
```

重新打开Atom，可以看到Nulibe的主界面：

##加载React Native 工程
`Add Porject Folder` 可以加载本地的React Native项目。
`Add Remote Project Folder`可以加载远程的git的项目。
可以看到React Native的文件目录。
可以方便的修改Java和Object-c代码，对于这三种代码都有比较友好的代码提示。


##React Native服务
使用 `command+shift+p`可以看到命令菜单，这里可以启动React Native的服务。
启动服务：
还可以通过`Nulide React Natvie:Start Debugging`启动debugging。

##运行项目
现在我们已经启动了React Native的服务，直接在项目目录下面运行
>react-native run-ios
>react-native run-android
可以直接运行到ios设备和android设备上。




