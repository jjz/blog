#CentOS 安装 Jenkins
Jenkins是开源的Java编写的持续集成的工具，在Centos上可以通过`yum`命令行直接安装。
##安装Java
看到当前系统java版本。
>java -version

如果java不存在运行命令：

>sudo yum install java

如果java不存在可以安装java,如果java存在可以升级java。
##安装Jenkins
添加Jenkins源:
>sudo wget -O /etc/yum.repos.d/jenkins.repo http://jenkins-ci.org/redhat/jenkins.repo 
>sudo rpm --import http://pkg.jenkins-ci.org/redhat/jenkins-ci.org.key

安装Jenkins:
>yum install jenkins

##启动Jenkins
输入命令启动Jenkins:
>sudo service jenkins start 
```
Starting Jenkins                                           [  OK  ]
```
在浏览器中输入：http://127.0.0.1:8080/ 就可以使用jenkins了,如果没有UI系统可以在其他的浏览器中输入http://<所在机器的ip>:8080/进入Jenkins界面 。
>sudo service jenkins stop
可以停止Jenkins服务。

##相关配置
Jenkins安装目录：
>cd /usr/lib/jenkins/
```
jenkins.war
```
jenkins.war安装地址。

Jenkins配置文件：
>cd /etc/sysconfig
Jenkins的配置文件在这里,名字是`jenkins`
>cat jenkins
可以查看jenkins默认的配置。这里介绍下三个比较重要的配置目录`JENKINS_HOME `,`JENKINS_USER `,`JENKINS_PORT `
```
## Path:        Development/Jenkins
## Description: Jenkins Continuous Integration Server
## Type:        string
## Default:     "/var/lib/jenkins"
## ServiceRestart: jenkins
#
# Directory where Jenkins store its configuration and working
# files (checkouts, build reports, artifacts, ...).
#
JENKINS_HOME="/var/lib/jenkins"
```
Jenkins储存文件的地址,Jenkins的插件，生成的文件都会放在这里。
```
## Type:        string
## Default:     "jenkins"
## ServiceRestart: jenkins
#
# Unix user account that runs the Jenkins daemon
# Be careful when you change this, as you need to update
# permissions of $JENKINS_HOME and /var/log/jenkins.
#
JENKINS_USER="jenkins"
```
Jenkins使用的用户，默认为`jenkins`

```
## Type:        integer(0:65535)
## Default:     8080
## ServiceRestart: jenkins
#
# Port Jenkins is listening on.
# Set to -1 to disable
#
JENKINS_PORT="8080"
```
Jenkins的端口，默认端口是8080。

