#Jenkins持续集成Android
Jenkins是一个基于Java的开源的CI项目:http://jenkins-ci.org/。它包括持续的软件版本发布/测试，监控外部调用执行的工作等...
在开发Andorid项目的时候，常常需要build新的APK,给内部人员或者外部人员测试使用,还有就是运行单元测试等...执行`gradle build`本身又比较费时。可以借助Jenkines完成自动打包，发布的工作。

##安装Jenkins
在这里:http://jenkins-ci.org/可以下载最新的Jenkins安装包。
下载完成之后开始安装:
![图片描述][1]

一路*Continue*

安转完成之后，会直接启动Jenkins，在浏览器中可以看到：
![图片描述][2]

也可以通过命令启动:
> java -jar jenkins.war

##安装插件
需要安装插件`git`,`gradle`,`android`三个插件，`android`插件可以帮助下载android sdk如果已经有android sdk可以不用安转该插件。
在右侧的菜单栏中的`系统管理`里面可以找到`管理插件`，在`可选插件`中搜索`gradle`
系统管理:
![图片描述][3]
管理插件:
![图片描述][4]
安装 `Gradle plugin`,`Git Parameter Plug-In`,`Android Emulator Plugin`插件。
安装完成之后重启Jenkins
![图片描述][5]

##创建任务
创建一个新任务，选择构建一个自由风格的软件项目：
![图片描述][6]

选择git:
![图片描述][7]
在构建环境中选择gradle构建项目：
![图片描述][8]
使用系统的gradle,执行一个任务:
![图片描述][9]

选择`archive the artifacts`
输入:example/build/outputs/apk/*.apk用来保存编译的Apk文件。
![图片描述][10]

##开始构建
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
可以找到配置gradle：
![图片描述][11]
 
构建成功:

![图片描述][12]

成功了构建出了Apk文件。

用于自动构建的项目地址：https://github.com/jjz/android-crop
*下一篇会聊聊遇到的坑*


  [1]: /img/bVtz3b
  [2]: /img/bVtz3u
  [3]: /img/bVtz5d
  [4]: /img/bVtz4n
  [5]: /img/bVtz42
  [6]: /img/bVtz5l
  [7]: /img/bVtz5x
  [8]: /img/bVtz5F
  [9]: /img/bVtz5V
  [10]: /img/bVtz55
  [11]: /img/bVtz6K
  [12]: /img/bVtz6W




