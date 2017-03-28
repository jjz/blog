# Meteor构建桌面应用
之前写了如何是`Meteor`如果构建ios和Android应用,[Meteor构建Android应用](http://www.jianshu.com/p/aa5d99401764)和[Meteor构建ios应用](http://www.jianshu.com/p/09e908444689),Meteor出了能使用在Web，ios，Android上面以外，还能用来构建桌面应用。
## 怎么样构建桌面应用？
使用`Meteor`能够构建桌面系统，还要依赖与`Nodejs`的快速发展。
`Nodejs`有几个可以构建桌面应用的类库,主要使用的有`atom-shell`(改名为 `Electron`)，`NW.js`。
>`atom-shell` 是 `GitHub` 随 `Atom` 一起开源的跨操作系统（Windows，Linux，MacOS X）的利用 Web 技术（Node.js、JavaScript、HTML 5）开发桌面应用的框架。`Atom`就是构建在 atom-shell 之上。

`Atom`就是最近比较火一款文本编辑器

>`node-webkit`（NW.js）  基于`node.js`和`chromium`的应用程序实时运行环境，可运行通过HTML(5)、CSS(3)、Javascript来编写的本地应用程序。node.js和webkit的结合体，webkit提供DOM操作，node.js提供本地化操作；且将二者的context完全整合，可在HTML代码中直接使用node.js的API。

## Atom-Shell和NW.js的区别

* `程序的入口不同`：`Node-Webkit` 的程序入口是一个网页，`atom-shell`的程序入口是一个*JavaScript脚本*
* `编译系统不同`：`atom-shell` 使用`libchromiumcontent`访问 `Chromium` 的 Content API，这样编译 `atom-shell` 的时候就不用编译整个 Chromium （编译 Chromium 非常费时）。
* `Node集成`不通:
`NW.js` 的 Node 集成需要给 Chromium 打补丁才能工作。`atom-shell` 通过集成 libuv loop 和 平台的 message loop 避免给 Chromium 打补丁。
*  Multi-context:
`Node-Webkit` 创造了 Node context 和 web context 的概念，而 `atom-shell` 没有引入新的 context，而是直接使用 Node 的 Multi-context 特性
 
** `GitHub`最初考察了 `Node-Webkit`,最终还是选择自己写`Atom-Shell`
 这里我们也使用`Atom-Shell` 即`Electron`**
 

## 安装Electron
 >npm install electron-prebuilt -g

 `-g`的意思可把Electron安装到本地系统中。
 
查看`Electron`版本
 > electron -v
  
```
  v0.32.1
 ```
## 在MacOS和Linux上构建Meteor App
  可以参考开源的例子 [https://github.com/sircharleswatson/Electrometeor](https://github.com/sircharleswatson/Electrometeor)
```
 git clone git@github.com:sircharleswatson/Electrometeor.git Electrometeor
 cd Electrometeor
 npm install
```
 等待安装完对应的包之后 ，开始运行 :
>node ./script/setup.js

 会安装缺少的类库，然后运行 :
>node ./script/run.js
 
 运行效果:

![运行效果](http://upload-images.jianshu.io/upload_images/22188-46cce6357dca923a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 
 
 ...还有windows...以后再写~

