# React Native 的开发工具:Nuclide
做React Native开发的时候,没有一个好用的IDE,Android Studio打开Android下的项目还可以,JS部分的代码打开并不方便,xcode大家OC项目还可以,对于其他的语言也不友好。也没有一个比较好的方式运行React Natvie的服务,每次Run的时候都需要启动一个React Native的服务，这样就会降低开发的效率。
还好有`Nuclide`,Nuclide是Facebook专门为React开发的IDE。Nuclide拥有更好的语法补全，类型检查等,Nulide其实是在Atom的基础上的一系列的插件集，Atom是Github打造的下一代编程开发利器，是一个完全用JavaScript开发的编辑器。
首先要先安装Atom，下载地址：https://atom.io/

## 安装Nuclide

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
![图片描述][1]

## 加载React Native 工程
右上角的菜单里面:
`Add Porject Folder` 可以加载本地的React Native项目。
`Add Remote Project Folder`可以加载远程的git的项目。
加载下本地已经存在的`React Natvie`的项目:
![图片描述][2]
可以看到React Native的文件目录:
![图片描述][3]
可以方便的修改JavaScript,Java和Object-c代码，对于这三种代码都有比较友好的代码提示。


## React Native服务
使用 `command+shift+p`可以看到命令菜单，这里可以启动React Native的服务。
启动服务:
![图片描述][4]

还可以通过`Nulide React Natvie:Start Debugging`启动debugging:
![图片描述][5]

## 运行项目
现在我们已经启动了React Native的服务，直接在项目目录下面运行:
>react-native run-ios
>react-native run-android

可以直接运行到ios设备和android设备上。


  [1]: /img/bVs08p
  [2]: /img/bVs08Z
  [3]: /img/bVs083
  [4]: /img/bVs09a
  [5]: /img/bVs09x




