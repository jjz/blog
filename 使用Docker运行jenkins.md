# 使用Docker运行jenkins
**Docker** 是一个开源的应用容器引擎，一种虚拟化技术。
**Jenkins**是一个开源的持续集成工具使用java开发。
首先要安装Docker:https://segmentfault.com/a/1190000004556081
安装完成之后启动docker：
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
这里会给一个ip地址`192.168.99.100`这个就是分配给docker虚拟机的ip地址。

然后去pull *jenkins*的docker镜像:
>docker pull jenkins

等待镜像下载完成，就可以运行jenkins的镜像了.

>docker run -d -p 8080:8080  -v $PWD/jenkins:/var/jenkins_home -t  jenkins

把`/var/jenkins_home`映射到 'jenkins/'目录下。
把jenkins的8080端口暴露给容器的8080端口,可以在容器外访问。


运行效果:
![图片描述][1]


  [1]: /img/bVthCU




