# Jenkins使用Git Submodule

项目的版本库在一些情况下需要引用其他的版本库，例如公用的代码库，可以在多个项目中调用，而公用库本身也是一个git的版本库，这个时候可以使用git子模组（`Git Submodule`）解决，`Git Submodule`允许将一个git repostory以目录的形式做为另一个git repostory的子目录。可以参考:[https://segmentfault.com/a/1190000003076028](使用Git Submodule管理子模块)

Jenkins用来做项目的持续集成，如果使用了Git Submodule,每次Jenkins更新版本库的时候还需要更新submodule的内容。

## 设置项目更新submodule
在项目的设置 -> 源码管理 -> git中，添加一个`Additional Behaviours`：
![图片描述][1]

选择`Recursively update submodules`，会递归的更新Git Sbumodule中的内容。

开始构建,项目中出现一个错误出现:
```
hudson.plugins.git.GitException: Command "git.exe submodule update --init --recursive" returned status code 128:
stdout: 
stderr: Cloning into '...'...
Permission denied (publickey).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
fatal: clone of '....' into submodule path '....' failed

    at org.jenkinsci.plugins.gitclient.CliGitAPIImpl.launchCommandIn(CliGitAPIImpl.java:1693)
    at org.jenkinsci.plugins.gitclient.CliGitAPIImpl.access$500(CliGitAPIImpl.java:62)
    at org.jenkinsci.plugins.gitclient.CliGitAPIImpl$7.execute(CliGitAPIImpl.java:953)
    at hudson.plugins.git.extensions.impl.SubmoduleOption.onCheckoutCompleted(SubmoduleOption.java:90)
    at hudson.plugins.git.GitSCM.checkout(GitSCM.java:1098)
    at hudson.scm.SCM.checkout(SCM.java:485)
    at hudson.model.AbstractProject.checkout(AbstractProject.java:1276)
    at hudson.model.AbstractBuild$AbstractBuildExecution.defaultCheckout(AbstractBuild.java:607)
    at jenkins.scm.SCMCheckoutStrategy.checkout(SCMCheckoutStrategy.java:86)
    at hudson.model.AbstractBuild$AbstractBuildExecution.run(AbstractBuild.java:529)
    at hudson.model.Run.execute(Run.java:1738)
    at hudson.matrix.MatrixBuild.run(MatrixBuild.java:301)
    at hudson.model.ResourceController.execute(ResourceController.java:98)
    at hudson.model.Executor.run(Executor.java:410)
Finished: FAILURE
```

## Git Submodule的身份认证
上面错误产生的原因git submodule使用的是ssh方式管理repository,没有找到可以使用的`ssh key`做submodule的身份认证。
这个问题已经在Jenkins的官网上提了个**issue**[https://issues.jenkins-ci.org/browse/JENKINS-20941](https://issues.jenkins-ci.org/browse/JENKINS-20941)。
目前解决这个问题的正式版本还没有发布，只能使用beta的插件解决这个问题，需要两个插件：
	1.git 2.5.0-beta5
	2.git-client 1.20.0-beta3

## Jenkins插件升级
在系统管理中，可以找到插件管理:
![图片描述][2]

这里面有两个选项，一个是直接上传插件，可以把`git 2.5.0-beta5`和`git-client 1.20.0-beta3`下载到本地再直接上传,就可以完成插件更新。
也可以设置更新站点，更新插件（一般情况下这种方式不是太容易更新，原因你知道的）。

更新地址：
```
http://mirror.xmission.com/jenkins/updates/experimental/update-center.json
```
## 使用统一的身份验证
插件更新完成之后，回到项目配置中git的设置，会多出一个选项:
![图片描述][3]

`User credentials from default remote of parent repository`
意思是`Git Submodule`的repository会使用和主repository一样的验证，就是主repository使用的身份，选中之后，Git Submodule就可以正常更新了。
再次构建项目,可以看到submodule的更新信息：
```
....
 git submodule init #  timeout=10
 > git submodule sync #  timeout=10
 > git config --get remote.origin.url #  timeout=10
 > git config --get-regexp ^submodule #  timeout=10
 > git config --get submodule.test.url #  timeout=10
 > git remote #  timeout=10
 > git config --get remote.origin.url #  timeout=10
using GIT_SSH to set credentials ...
 > git submodule update --init --recursive test
 ....
```


  [1]: /img/bVvdG0
  [2]: /img/bVvdHw
  [3]: /img/bVvdH0



