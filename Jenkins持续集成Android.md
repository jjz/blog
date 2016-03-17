#Jenkins持续集成Android
Jenkins是一个基于Java的开源的CI项目:http://jenkins-ci.org/。包括持续的软件版本发布/测试，监控外部调用执行的工作。
在开发Andorid项目的时候，常常需要build新的APK,给内部人员或者外部人员测试，还有就是运行单元测试等。执行`gradle build`本身又比较费时。可以借助Jenkines完成自动打包，发布的工作。
##安装Jenkins
在这里:http://jenkins-ci.org/可以下载最新的Jenkins安装包。

一路*Continue*

安转完成之后，会直接启动Jenkins，在浏览器中可以看到：

也可以通过命令启动:
> java -jar jenkins.war

##安装gradle插件
在右侧的菜单栏中的`系统管理`里面可以找到`管理插件`，在`可选插件`中搜索`gradle`


再安装`git`插件

安装完成之后重启Jenkins


##创建任务
创建一个新任务，选择构建一个自由风格的软件项目：

选择git:
选择gradle构建项目：

使用系统的gradle,执行一个任务

选择`archive the artifacts`

输入:*/build/outpusts/apk/*.apk用来保存编译的Apk文件。


开始第一次构建：
有一个错误产生：
```
[Gradle] - Launching build.
[workspace] $ gradle clean build run
FATAL: command execution failed
java.io.IOException: Cannot run program "gradle" (in directory "/Users/Shared/Jenkins/Home/jobs/android-crop/workspace"): error=2, No such file or directory
```
找不到`gradle`，需要在系统中配置下gradle。
在`系统管理`->`系统设置`中:

可以找到配置

配置本地gradle
 

注意这个配置一定要保证gradler的版本和build.gradle中的`        classpath 'com.android.tools.build:gradle:2.1.0-alpha1'
`版本一致。


错误：
```
example/bulid.gradle

FAILURE: Build failed with an exception.

* Where:
Build file '/Users/Shared/Jenkins/Home/jobs/android-crop/workspace/build.gradle' line: 12

* What went wrong:
A problem occurred evaluating root project 'workspace'.
> Could not find property 'VERSION' on root project 'workspace'.

```
gradle.properties没有提交




