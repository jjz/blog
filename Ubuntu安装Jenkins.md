#Ubuntu安装Jenkins

在Ubuntu上安装Jenkins的持续集成环境。参考地址：http://blog.csdn.net/ruisheng_412/article/details/8066330

## 安装

下载Debian package的repository,并安装到系统中，方便安装和升级:
>wget -q -O - http://pkg.jenkins-ci.org/debian-stable/jenkins-ci.org.key | sudo apt-key add

添加Jenkins的源到`/etc/apt/sources.list`:

>sudo sh -c 'echo deb http://pkg.jenkins-ci.org/debian binary/ > /etc/apt/sources.list.d/jenkins.list'  

更新本地包，安装jenkins:
>sudo apt-get update
>sudo apt-get install jenkins

## 启动
启动Jenkins:
>sudo /etc/init.d/jenkins start

关闭Jenkins:

>sudo /etc/init.d/jenkins stop

配置文件地址为:`/etc/default/jenkins`



