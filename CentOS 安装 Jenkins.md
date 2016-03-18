#CentOS 安装 Jenkins
Jenkins是开源的Java持续集成的库，可以通过`yum`命令行直接安装。
##安装Java
>java -version
可以看到当前系统的java版本。
>sudo yum install java
如果java不存在可以安装java,如果java存在且升级。
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
在浏览器中输入：http://127.0.0.1:8080/ 就可以使用jenkins了,如果没有UI系统可以在其他的浏览器中输入http://<所在机器的ip>:8080/进入Jenkins界面 

##相关配置
Jenkins安装目录：
>cd /usr/lib/jenkins/
```
jenkins.war
```
Jenkins配置文件地址：
>cd /etc/sysconfig
Jenkins的配置文件就在这里,名字是`jenkins`
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
Jenkins文件存在的位置。
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
Jenkins使用的用户。

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


