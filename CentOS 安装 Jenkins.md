`Jenkins`是开源的,使用`Java`编写的持续集成的工具，在Centos上可以通过`yum`命令行直接安装。记录下安装的过程，方便以后查找。需要先安装`Java`,如果已经`Java`可以跳过该步骤。
##安装Java
看到当前系统`Java`版本的命令:
>java -version

如果显示`Java`版本号，说明已经正确安装，如果显示没有该命令，需要安装`Java`：
>sudo yum install java

该命令如果检测到`Java`不存在可以直接安装`Java`,如果已存在则可以升级`Java`。
##安装Jenkins
首先要先添加`Jenkins`源:
>sudo wget -O /etc/yum.repos.d/jenkins.repo http://jenkins-ci.org/redhat/jenkins.repo 
>sudo rpm --import http://pkg.jenkins-ci.org/redhat/jenkins-ci.org.key

添加完成之后直接使用`yum`命令安装`Jenkins`:
>yum install jenkins

##启动Jenkins
使用命令启动`Jenkins`:
>sudo service jenkins start 

    Starting Jenkins                                           [  OK  ]

在浏览器中输入：[http://<服务器ip>:8080/](http://<服务器ip>:8080/) 就可以进入`Jenkins`界面直接使用了 。
停止`Jenkins`服务的命令为：
>sudo service jenkins stop


##相关配置
`Jenkins`安装目录：
      
     /var/lib/jenkins/

Jenkins配置文件地址：
    
    /etc/sysconfig/jenkins

这就是`Jenkins`的配置文件，可以在这里查看`Jenkins`默认的配置。
>cat jenkins

这里介绍下三个比较重要的配置：

* `JENKINS_HOME `
* `JENKINS_USER ` 
* `JENKINS_PORT `

`JENKINS_HOME`是Jenkins的主目录，Jenkins工作的目录都放在这里,Jenkins储存文件的地址,Jenkins的插件，生成的文件都在这个目录下。

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

`JENKINS_USER ` 是Jenkins的用户，拥有$JENKINS_HOME和/var/log/jenkins的权限。

    ## Type:        string
    ## Default:     "jenkins"
    ## ServiceRestart: jenkins
    #
    # Unix user account that runs the Jenkins daemon
    # Be careful when you change this, as you need to update
    # permissions of $JENKINS_HOME and /var/log/jenkins.
    #
    JENKINS_USER="jenkins"


`JENKINS_PORT `是Jenkins的端口，默认端口是8080。


    ## Type:        integer(0:65535)  
    ## Default:     8080
    ## ServiceRestart: jenkins
    #
    # Port Jenkins is listening on.
    # Set to -1 to disable
    #
    JENKINS_PORT="8080"

