# MacOS上安装Docker
Docker引擎是基于Linux内核特性的开源应用容器。让开发者可以打包他们的应用以及依赖包到一个可移植的容器中，发布到任何流行的Linux容器上，实现虚拟化，容器运行在类似与iphone的沙盒中。
在MacOs上面运行Docker先安装`Docker Toolbox`。

## Docker Toolbox
Docker Toolbox是获取以及运行Docker生产环境最快捷的方式，他包括：
    1.Docker Machine 
    2.Docker
    3.docker-compose
    4.Docker GUI 
    5.Docker command-line
    6.VirtualBox
因为Docker是以及Linux内核的，所以不能直接在OS X上面运行，因此我们需要VirtualBox，因为一个虚拟的Docker环境。
环境要求：OS X 10.8以上的，才能安装运行`Docker Toolbox`。
## 安装
下载地址：https://www.docker.com/products/docker-toolbox
下载之后双击安装文件开始安装。

安装界面:
![图片描述][1]
点击`Continue`可以看到要安装的工具:
![图片描述][2]

默认情况下Docker Toolbox会安装在/usr/local/bin目录下面。我们需要让所有的用户都有访问`Docker Toolbox`安装文件的权限。
会出现让我们输入密码的界面:
![图片描述][3]

最后会安装两个快速启动的工具:
![图片描述][4]

## hello world 
双击Application的`Docker Quickstart Terminal`:
![图片描述][5]
选择一个常用的终端,我选择的是iTerm。
等待初始化完成之后出现下面的界面
```


                        ##          .
                  ##  ##  ##         ==
               ##  ##  ##  ##  ##     ===
           /"""""""""""""""""\___/ ===
      ~~~ {~~ ~~~~ ~~~ ~~~~ ~~~ ~ /  ===- ~~~
           \______ o           __/
             \    \         __/
              \____\_______/


docker is configured to use the default machine with IP 192.168.99.100
For help getting started, check out the docs at https://docs.docker.com

Welcome to fish, the friendly interactive shell
Type help for instructions on how to use fish
```
Docker的初始化运行已经完成了，下面试试经典的`hello world`。

运行命令:
>docker run hello-world

得到一个错误信息:
```
docker: Error response from daemon: client is newer than server (client API version: 1.22, server API version: 1.21).
See 'docker run --help'.
```
明显的 docker server的版本过低，升级下 docker server 
> docker-machine upgrade

再次运行:
>docker run hello-world
得到结果:
```
Hello from Docker.
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker Hub account:
 https://hub.docker.com

For more examples and ideas, visit:
 https://docs.docker.com/userguide/
 
```
安装Docker完成。


  [1]: /img/bVtho1
  [2]: /img/bVtho2
  [3]: /img/bVtho3
  [4]: /img/bVtho7
  [5]: /img/bVthpa

