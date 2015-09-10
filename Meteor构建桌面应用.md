# Meteor构建桌面应用
##怎么样构建桌面应用
Nodejs有几个可以构建桌面应用的类库,主要用到的是`atom-shell`(改名为 `Electron`)，`NW.js`
等。
`atom-shell` 是 `GitHub` 随 `Atom` 一起开源的跨操作系统（Windows，Linux，MacOS X）的利用 Web 技术（Node.js、JavaScript、HTML 5）开发桌面应用的框架。Atom即构建在 atom-shell 之上。
`node-webkit`（NW.js）  基于`node.js`和`chromium`的应用程序实时运行环境，可运行通过HTML(5)、CSS(3)、Javascript来编写的本地应用程序。node.js和webkit的结合体，webkit提供DOM操作，node.js提供本地化操作；且将二者的context完全整合，可在HTML代码中直接使用node.js的API。

##Atom-Shell和NW.js的区别
`程序的入口不同`：Node-Webkit 的程序入口是一个网页，atom-shell的程序入口是一个JavaScript脚本
`编译系统不同`：atom-shell 使用`libchromiumcontent`访问 Chromium 的 Content API，这样编译 atom-shell 的时候就不用编译整个 Chromium （编译 Chromium 非常费时）。
`Node集成`:
NW.js 的 Node 集成需要给 Chromium 打补丁才能工作。atom-shell 通过集成 libuv loop 和 平台的 message loop 避免给 Chromium 打补丁。
 Multi-context:
Node-Webkit 创造了 Node context 和 web context 的概念，而 atom-shell 没有引入新的 context，而是直接使用 Node 的 Multi-context 特性
 
 GitHub最初考察了 Node-Webkit,最终还是选择自己写Atom-Shell
 这里我们也使用Atom-Shell 即Electron
 
##安装Electron
 >npm install electron-prebuilt -g
 `-g`的意思可以吧electron作为命令使用使用
 
 > electron -v
  
      v0.32.1
  
##在MacOS和Linux上构建Meteor App
  这里有个开源的例子
  [https://github.com/sircharleswatson/Electrometeor][1]

    git clone git@github.com:sircharleswatson/Electrometeor.git Electrometeor
    cd Electrometeor
    npm install
 安装完对应的包之后
 开始运行 
>node ./script/setup.js
 会安装缺少的类库
 然后运行 
>node ./script/run.js
 
 运行效果
 ![MacOS运行效果][2]
 
 
 ...还有windows...下次再写
 


  [1]: https://github.com/sircharleswatson/Electrometeor
  [2]: /img/bVpOgk

