#CentOS上面搭建Docker
在CentOS上面搭建一个Docker,不要为我为什么不用Ubuntu（因为某种我不想说的原因^_^）。
##环境要求
Docker要求系统必须在CentOS 6以上，内核版本必须是2.6.32-431以上
查看CentOS版本:
>cat /etc/issue
```
CentOS release 6.7 (Final)
Kernel \r on an \m
```
查看内核版本:
>uname -r
```
2.6.32-573.el6.x86_64
```
##安装Docker
运行命令:
>yum update
>sudo yum install docker-io

第一条命令是更新下现有库。

##启动Docker
>sudo service docker start

```
tarting cgconfig service:                                 [  OK  ]
Starting docker:	                                   [  OK  ]
```
测试下Docker安装是否成功,使用著名的`hello world`:
>docker run hello-world

```
latest: Pulling from hello-world
3f12c794407e: Pull complete
975b84d108f1: Pull complete
Digest: sha256:8be990ef2aeb16dbcb9271ddfe2610fa6658d13f6dfb8bc72074cc1ca36966a7
Status: Downloaded newer image for hello-world:latest

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
安装成功！



