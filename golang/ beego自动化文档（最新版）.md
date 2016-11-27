之前写过一篇使用Beego自动化api文档的文章：[Beego自动化文档](http://www.jianshu.com/p/0d375f4b36b2),随着Beego的更新，1.7.0之后Beego自动化文档的方法也有了更新，最显著的更新是去掉了`docs.go`，使用了`swagger.json`,更加的符合swagger的特点。这篇文章是上一篇文章的修正和补充。
##环境要求
需要安装最新的Go语言环境，安装Go可以参考[Golang在Mac OS上的环境配置](http://www.jianshu.com/p/943870134593),还需要安装最新的Beego框架。如果是你的Beego框架还是旧版本的就需要升级Beego:
>go get -u github.com/astaxie/beego
>go get -u github.com/beego/bee

查看bee的最新版本：
>bee version

```
| ___ \
| |_/ /  ___   ___
| ___ \ / _ \ / _ \
| |_/ /|  __/|  __/
\____/  \___| \___| v1.5.2

├── Beego     : 1.7.1
├── GoVersion : go1.7.3
├── GOOS      : darwin
├── GOARCH    : amd64
├── NumCPU    : 8
├── GOPATH    : /Users/jjz/Documents/go
├── GOROOT    : /usr/local/Cellar/go/1.7.3/libexec
├── Compiler  : gc
└── Date      : Saturday, 26 Nov 2016
```
可以从该命令中看到Go的环境配置，Beego的版本等信息。
##文档的生成
在`conf/app.conf`中设置`EnableDocs=true`之后，仍然可以通过命令生成文档:
>bee generate docs

只是这里生成的不再是`docs.go`，而是符合`swagger`使用的两个文档:
```
swagger.json
swagger.yml
```
`swagger.yml`是swagger的契约文档，根据这份文档，可以描述出api的定义规则。而`swagger.json`描述的是一份符合swagger规则的api数据，通过这个json数据可以在`swagger-ui`中列出api文档。
使用`bee generate docs`命令可以生成对于对于的两个swagger文件，但是bee项目运行的时候是通过监控文件，自动重新编译项目的，自动重新编译项目也能自动生成文档是最好的，因此Beego在运行项目的时候添加了自动生成文档的命令:
>bee run -gendoc=true

这样可以在每次项目重新运行的时候更新api文档，不用重新运行命令:`bee generate docs`。
##API文档的访问
更新`swagger-ui`是一件很麻烦的事情，所以在beego的运行命令中加入一个参数`-downdoc=true`:
>bee run -downdoc=true

该命令会监测`swagger`目录下面是否有`swagger-ui`的文件，如果没有就从github上面下载，下载的地址是: [https://github.com/beego/swagger/archive/v2.zip](https://github.com/beego/swagger/archive/v2.zip)
另外设置静态文件夹的方法也有了变化，新的函数为:
>beego.SetStaticPath("/swagger", "swagger")

设置完静态文件夹之后可以通过url:[http://127.0.0.1:8080/swagger/index.html](http://127.0.0.1:8080/swagger/index.html)访问swagger文档。打开该文档会自动的请求`swagger.json`，发现请求的`swagger.json`文档地址为:[http://127.0.0.1:8080/swagger/index.html/swagger.json](http://127.0.0.1:8080/swagger/index.html/swagger.json),需要把`swagger/index.html`中的swagger.json的地址设置为：
>url = "/swagger/swagger.json";

再次访问swagger文档地址就可以看到API文档了：

![swagger文档](http://upload-images.jianshu.io/upload_images/22188-a22360ab04107fca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##路由解析与注解
路由解析仍然使用的是`namespace+Include`：
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
而注解也是仍然采用以前的注解方式:
```
// @Title createUser
// @Description create users
// @Param	body		body 	models.User	true		"body for user content"
// @Success 200 {int} models.User.Id
// @Failure 403 body is empty
// @router / [post]
func (u *UserController) Post() {
	var user models.User
	json.Unmarshal(u.Ctx.Input.RequestBody, &user)
	uid := models.AddUser(user)
	u.Data["json"] = map[string]string{"uid": uid}
	u.ServeJSON()
}
```
##总结
新版本的beego中对于swagger的支持更加的友好，也更加的swagger化了，采用了`swagger.json`和`swagger.yml`文件，不需要重新生成一个新的router文件了，这样文档部分的代码更加的分离，使用`swagger.yml`可以更方便的生成访问api的其他语言的代码。

源代码地址：[https://github.com/jjz/go/tree/master/autodoc](https://github.com/jjz/go/tree/master/autodoc)

