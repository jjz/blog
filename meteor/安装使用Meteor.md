# Meteor是什么
## Meteor是什么
`Meteor`是一种最新的`JavaScript`框架，用于自动化和简化实时运行的Web应用程序的开发，它使用分布式数据协议-`(Distributed Data Protocol,DDP)`的协议来处理实时通信，使用支持`WebSocket`的浏览器以及使用`Asynchronous JavaScript +XML(Ajax)`长轮询的旧浏览器支持这种协议，浏览器到服务器的通信是透明的。

`Meteor`提供了两个`MongoDB`数据库：一个客户端缓存库和服务器上的一个`MongoDB`数据库。当一个用户更改数据时，在浏览器中运行的`JavaScript`代码会更新本地的`MongoDB`中的相应的数据库项，然后向服务器发出一个`DDP`请求。
该代码会立即执行,就和服务器执行成功那样继续运行，因为它不需要等待服务器回复。与此同时，服务器在后台更新。如果服务器操作失败或者返回一个以为结果，那么客户端`JavaScript`代码会依据从服务器新返回的数据立即进行调整。这种调整称为延迟补偿，向用户提供了更高的认知速度。

甚至连 `Meteor` 的模板系统也是为简化实时通信而设计的，Meteor中的模板系统会根据用户记录在模板中的数据，自动回调，以便在底层数据更改时调用此`HTML`，使实时模板变得更加简单快捷。
`Meteor`网站地址:[https://www.meteor.com/](https://www.meteor.com/)


##安装Meteor
运行命令：

>curl https://install.meteor.com/ | sh

```
    eteor 1.1.0.3 has been installed in your home directory (~/.meteor).
    Writing a launcher script to /usr/local/bin/meteor for your convenience.
    This may prompt for your password.
    
    To get started fast:
    
      $ meteor create ~/my_cool_app
      $ cd ~/my_cool_app
      $ meteor
    
    Or see the docs at:
    
      docs.meteor.com
```
  
##新建Meteor项目
使用`meteor create`可以创建一个新项目:
  >meteor create test-meteor
  
```
    test-meteor: created.
    
    To run your new app:
      cd test-meteor
      meteor
 ```    

#运行Meteor
 运行`meteor`可以直接运行`meteor`项目:
> meteor
 
 ```

    [[[[[ ~/Documents/workspace/node/test-meteor ]]]]]
    
    => Started proxy.
    => Started MongoDB.
    => Started your app.
    
    => App running at: http://localhost:3000/
```
在浏览器中输入 http://localhost:3000/ 就可以看到一个网页：


![meteor](http://upload-images.jianshu.io/upload_images/22188-74c17b6ad204559e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

