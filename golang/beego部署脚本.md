# beego自动化部署
`beego`是Golang写的应用程序开源框架http://beego.me/，我使用`beego`写了一个项目小项目，golang是编译语言，需要编译之后再部署，每次部署到服务上是件很麻烦的事情。就写了一个自动打包部署到服务器的脚本，方便发布部署。

## 项目结构
先看看beego项目的结构：
```
beepkg
|-- conf
|   `-- app.conf
|-- controllers
|   `-- default.go
|-- main.go
|-- models
|-- routers
|   `-- router.go
|-- static
|   |-- css
|   |-- img
|   `-- js
|-- tests
|   `-- default_test.go
`-- views
    `-- index.tpl  
```
其中`controllers`，`views`.`models`以及其他的.go文件会被打包成可执行文件**beepkg**。
而静态文件`static`以及`conf`文件不会被编译，需要打包上传部署到服务器上。
因此在打包文件的时候需要忽略一些文件：
	1.go文件，已经编译成可执行文件
	2.本地生成的一些临时文件
	3.项目的一些配置文件
因此我们在使用`bee pack`打包的时候需要忽略这些文件，`bee pack`在编译之后压缩的的时候会默认忽略这三种文件:.go:.DS_Store:.tmp。因此我们只需要忽略其他的文件即可。
使用命令`-exr`用正则表达式忽略文件：
>bee pack -be GOOS=linux -ba -exr='^[0-9a-f]$'

因为使用了文件存储的session，本地目录下会生成session的文件夹需要忽略打包`[0-9a-f]`,在打包的时候需要忽略这些文件。


## 打包脚本
打包beego项目，同步到服务端，需要做三件事情:
	1.更新代码
	2.编译可执行文件，打包资源
	3.同步到压缩包到服务器
    4.服务器解压，重启应用程序
首先是第一步更新代码:
```
git reset HEAD --hard
git pull origin master
```
然后需要编译可执行文件和打包资源:
```
bee pack -be GOOS=linux  -ba -exr='^[0-9a-f]|[*.iml]$'
```
同步压缩包到服务器:
```
scp .tar.gz  jjz@192.168.1.10:/root/goapp/beepkg

```
解压和重启服务需要在服务器端执行，可以通过`ssh`执行在服务器端执行脚本:
```
ssh jjz@192.168.1.10 'bash -s' < restart.sh
```

## Supervisor
服务器端可以使用`tar`命令解压文件,但是如何重启应用程序呢,在服务器端可以使用`Supervisor`管理beego进程，重启应用程序。
`Supervisor`是一个*python*写的进程管理工具，用途就是有一个进程需要每时每刻不断的跑(web服务)，但是这个进程又有可能由于各种原因有可能中断。当进程中断的时候又希望能自动重启它，这个时候，我们就需要使用`Supervisor`，这里我们更多的是利用`Supervisor`的重启进程功能。

安装Supervisor:
>pip install supervisor

初始化配置文件:
>echo_supervisord_conf >/etc/supervisord.conf

修改配置文件`/etc/supervisord.conf`添加配置:
```
;指定进程名称
[program:beepkg]
directory=/opt/goapp/beepkg
command=/opt/goapp/beepkg
autostart=true
user=root
redirect_stderr=true
stdout_logfile =/var/log/supervidrod/beepkg.log
```
启动`Supervisor`：
>supervisord -c /etc/supervisord.conf

使用`supervisorctl`命令可以进入shell界面，管理`program`。
比如：
```
# 启动进程
strat beepkg
# 停止进程
stop beepkg
# 重启进程
restart beepkg
```
注意如果`Supervisor`的配置文件更改之后一定要使用`reload`才能使配置修改的配置文件生效。

在修改配置文件的过程中，遇到了一个错误
```
 * Starting Supervisor daemon manager...
    Error: Another program is already listening on a port that one of our HTTP servers is configured to use.  Shut this program down first before starting supervisord.
    For help, use /usr/bin/supervisord -h
   ...fail!
```
解决办法：
```
find / -name supervisor.sock
unlink /tmp/supervisor.sock
```
## 最终脚本
解压文件并重启服务的脚本`restart.sh`:
```
# ! /bin/bash
# 默认进入的是登录用户的目录
cd test/bee
tar -xzvf beepkg.tar.gz
# remove conf of dev
rm -rf conf/app.conf
cp conf/app.conf.bat conf/app.conf
supervisorctl restart beepkg
```

本地开发环境部署脚本`deploy.sh`:
```
# ! /bin/bash
echo 'update code'
git reset HEAD --hard
git pull origin master
echo 'pack'
bee pack -be GOOS=linux -exr='^[a-f0-9]$'
echo 'upload'
scp beepkg.tar.gz  jjz@192.168.1.10:/root/goapp/beepkg
echo 'restart'
ssh jjz@192.168.1.10 'bash -s' < restart.sh
```


