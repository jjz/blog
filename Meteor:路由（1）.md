# Meteor:路由（1）
Meteor本身并没有好用的路由使用，还有比较好用的第三方Package使用，`Iron.Router`就是一个比较好的Meteor的路由框架，提供了Client 和 Server的路由设置，还可以用来做`restful`风格的api使用。
##安装Iron.Router
直接cd到项目目录使用安装命令:
>meteor add iron:router
```
Changes to your project's package version selections:

iron:controller        added, version 1.0.12
iron:core              added, version 1.0.11
iron:dynamic-template  added, version 1.0.12
iron:layout            added, version 1.0.12
iron:location          added, version 1.0.11
iron:middleware-stack  added, version 1.0.11
iron:router            added, version 1.0.12
iron:url               added, version 1.0.11


iron:router: Routing specifically designed for Meteor
```
可以看到安装的详细内容。
可以使用命令直接更新*Iron.Router*
>meteor update iron:router


##使用Iron.Router
我们知道使用`meteor create meteor-project`的命令创建的项目会默认生成三个文件.*meteor-project.html*,*meteor-project.css*,*meteor-project.js*安装Iron.Router之后，运行项目会发现，页面展示错误提示内容：
```
Organize your Meteor application.
Router.route('/', function () {
  this.render('Home', {
    data: function () { return Items.findOne({_id: this.params._id}); }
  });
});
        
```
我们需要设置路由规则，首先要先`meteor`默认生成的文件。

##使用路由规则
1.head.html
Iron.router只需要设置一个`head.html`
```
<head>
    <meta charset="UTF-8">
    <title>Iron-router</title>
</head>

```
查看网页源代码可以看到*head*里面多了个**title**。

2.设置"/"页面
新建一个home.html页面
```
<template name="home">
    <h1>I am home</h1>
</template>
```
3.router.js
先建一个router.js文件。添加一个路由规则:
```
Router.route('/', function () {
    this.render('home');
});
```
这段代码的意思是在主目录下，我们指定访问名字为`home`的template。
现在页面就可以正常的现在home.html的内容了。

##简化写法
我们在新建一个content.html。
```
<template name="content">
    <h2>I am content</h2>
</template>
```
增加一条路由规则:
```
Router.route('/content');
```
这个规则的会自动的指定名字叫做**content**的template或者页面，这样简单的写法，让你不用实现function。

##REST
大多数据的网站都支持REST风格的API,Iron.Router不仅仅能够对Meteor的内容做路由，还能使用REST风格的Api。
添加一条router规则:
```
Router.route('/content/:_id', function () {
    var req = this.request;
    var res = this.response;
    res.end('this is string');
}, {where: 'server'});
```
`where:'server'`用来告诉Rotuer这个是服务器端的路由规则.
使用`curl`看下结果:
>curl  'http://localhost:3000/content/1'
返回的结果为:
```
this is string
```
并没有html和js文件。

项目地址：




