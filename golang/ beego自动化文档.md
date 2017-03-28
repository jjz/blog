# beego自动化文档
## beego是什么？
`beego`是一个快速开发*go应用*的**http框架**，`go 语言`技术大牛[ASTA谢](http://weibo.com/533452688?refer_flag=1005055013_)的开源项目。
`beego`可以用来快速开发*API*、*Web*以及*后端服务*等各种应用，是一个`RESTFul`的框架，主要设计灵感来源于`tornado`、`sinatra`、`flask`这三个框架，结合了Go本身的一些特性(`interface`、`struct`继承等)而设计的。
`beego`结合`swagger`就能实现自动化的文档。
## Swagger是什么？
`Swagger` 是一个规范和一套完整的框架，用于`生成`、`描述`、`调用`以及`可视化` *RESTful 风格*的 *Web 服务*。
`Swagger`的总体目标是使客户端和文件系统服务器以同样的速度来更新，`方法，参数和模型`紧密集成到服务器端的代码中，`允许API`始终保持同步。
`Swagger` 让部署管理和使用API从未如此简单。
## 自动文档的好处？
    1. 不用手动写文档了，通过注解就可以自动化文档
    2. 文档和代码同步更新，代码更新之后不需要再更新文档
    3. 浏览器友好
    4. 使用Swagger框架可以调试API，在浏览器端可以看到更多的`request`和`response`信息
## 使用指南
首先安装go:[http://www.jianshu.com/p/943870134593](http://www.jianshu.com/p/943870134593)
可以使用`intelliJ`作为go的IDE:[http://www.jianshu.com/p/943870134593]()
也可以使用`Atom`作用go的IDE:[http://www.jianshu.com/p/c1d8cf274ec7](http://www.jianshu.com/p/c1d8cf274ec7)

安装beego:[http://beego.me/quickstart](http://beego.me/quickstart)
使用`go get`安装beego:
>go get github.com/astaxie/beego

安装bee工具:
>go get github.com/beego/bee

未了方面可以把`$GOPATH/bin`加入到你的`$PATH`变量中:
>export PATH=$PATH:$GOPATH/bin

## 创建一个beego项目
使用`bee`工具可以方便的创建，管理，运行，打包`beego`项目:
>bee api beeapi

必须在`$GOPATH/src`的目录下创建项目。
为该项目指定`Swagger`目录:
>beego.StaticDir["/swagger"] = "swagger"

下载`Swagger`:[https://github.com/beego/swagger](https://github.com/beego/swagger)
也可以下载最新的`Swagger`:[http://swagger.io/](http://swagger.io/)

放到项目的`根目录`下面，目录名称为`swagger`，和上面的配置一致。
  ## 路由解析
目前自动化文档的路由规则只支持`NewNamespace`写法的解析，其他写法函数不会自动解析为文章，就是`namespace+Include`的写法。而且只支持二级解析，其中一级表示版本号，二级表示应用模块。
如：
```
    ns := beego.NewNamespace("/v1",
		beego.NSNamespace("/object",
			beego.NSInclude(
				&controllers.ObjectController{},
			),
		),
		beego.NSNamespace("/user",
			beego.NSInclude(
				&controllers.UserController{},
			),
		),
	)
	beego.AddNamespace(ns)
```
## 生成文档
在配置文件`conf/app.conf`中设置
  >  EnableDocs = true

生成`docs`文件:
>bee generate docs

文档的生成在 `docs`文件的`init函数`中调用的，因此必须在main中导入`docs`文件，这样就会调用docs的`init函数`
```  
  _ "beeapi/docs"
```
运行程序：
>bee run watchall true

 `bee run`命令是监控beego的项目文件，通过`fsnotify`监控文件系统，这样在开发的过程中可以实时的看到项目修改之后的效果。

打开[http://127.0.0.1:8080/swagger/]([http://127.0.0.1:8080/swagger/)就可以看到自动化文档的界面

![swagger](http://upload-images.jianshu.io/upload_images/22188-957e6318de300042.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

也可以运行下面命令改变docs的端口号：
>bee run docs -docport=8888

## 注解
`beego`的文档注解包括两种:全局注解和应用注解.
`全局注释`，必须放在`router.go`的最顶部,包括:
```
    @APIVersion
    @Title
    @Description
    @Contact
    @TermsOfServiceUrl
    @License
    @LicenseUrl
```
应用注释，需要放在对应方法的上面,包括:
```
    @title
    @Description
    @Param
    @Success
    @Failure
    @router 
```

测试项目地址：[https://github.com/jjz/beego_auto_doc](
https://github.com/jjz/beego_auto_doc)

参考文档：http://beego.me/docs/advantage/docs.md

 


