# beego自动化文档
##beego是什么？
`beego`是一个快速开发Go应用的http框架，go 语言方面技术大牛。beego可以用来快速开发API、Web、后端服务等各种应用，是一个RESTFul的框架，主要设计灵感来源于tornado、sinatra、flask这三个框架，但是结合了Go本身的一些特性(interface、struct继承等)而设计的一个框架。
##swagger是什么？
`Swagger` 是一个规范和完整的框架，用于`生成`、`描述`、`调用`和`可视化` RESTful 风格的 Web 服务。总体目标是使客户端和文件系统作为服务器以同样的速度来更新。文件的方法，参数和模型紧密集成到服务器端的代码，允许API来始终保持同步。Swagger 让部署管理和使用功能强大的API从未如此简单。
##自动文档的好处？
    1.不用手动写文档了，通过注释就可以自动生成文档
    2.文档和代码同步更新，不用更新文档了
    3.浏览器友好
    4.使用Swagger框架可以调试API，能看到更多的request和response信息
##环境配置
首先安装go
http://segmentfault.com/a/1190000003933557
可以再`intelliJ`作为go的ide
http://segmentfault.com/a/1190000003933657
安装beego
http://beego.me/quickstart
##创建一个api项目
>bee api beeapi

你必须在`$GOPATH/src`的目录下创建项目
该项目指定了Swagger
>beego.StaticDir["/swagger"] = "swagger"

下下载Swagger
https://github.com/beego/swagger
放到项目的`根目录`下面
    路由解析
目前自动化文档只支持如下的写法的解析，其他写法函数不会自动解析，即`namespace+Include`的写法，而且只支持二级解析，一级版本号，二级分别表示应用模块
如：
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

##生成文档
在配置文件中设置
>EnableDocs = true

生成docs文件
>bee generate docs

文档的生成在 docs文件的init函数中调用的，因此必须在main中导入
>_ "beeapi/docs"
运行程序：
>bee run watchall true
在[http://127.0.0.1:8080/swagger/][1] 地址下就可以看到自动化文档的界面
![图片描述][2]
也可以运行下面命令改变docs的端口号：
>bee run docs -docport=8888

##注释解析
全局注释，必须放在`router.go`中的最顶部
    @APIVersion
    @Title
    @Description
    @Contact
    @TermsOfServiceUrl
    @License
    @LicenseUrl

应用注释：
    @title
    @Description
    @Param
    @Success
    @Failure
    @router
参考文档：http://beego.me/docs/advantage/docs.md

测试项目地址：
https://github.com/jjz/beego_auto_doc


  [1]: http://127.0.0.1:8080/swagger/
  [2]: /img/bVqZSG

