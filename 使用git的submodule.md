使用Git  Submodule管理子模块

实例代码:
父项目:https://github.com/jjz/pod-project
子项目:https://github.com/jjz/pod-library
 
使用场景
基于公司的多项目，我们提取了一个公共的类库提供多个项目使用，这个library怎么和git在一起方便的管理呢？
1.如何在git项目中导入library库
2.library库在其他的项目中被修改了如何push
3.其他项目如何获取到library库最新的提交
4.如何在clone的时候能够自动导入library库
解决以上问题，我使用git 的submodule来解决。
什么是submodule
git submodule 是一个很好的项目协作工具，他允许类库项目做为repository,子项目做为一个单独的git项目存在父项目中，子项目可以有自己的独立的commit，push，pull。而父项目以submodule的形式包含子项目，父项目可以指定子项目header，父项目中会提交 submodule的信息，在clone父项目的时候可以把submodlue初始化。

在项目中增加Submodule
git submodule add git@github.com:jjz/pod-library.git pod-library
使用 git status命令可以看到 
git status
On branch master
Changes to be committed:

	new file:   .gitmodules
	new file:   pod-library
	
多了两个需要提交的文件 
gitmodules
[submodule "pod-library"]
	path = pod-library
	url = git@github.com:jjz/pod-library.git
记录子项目的 目录和git 信息
pod-libray
Subproject commit 4ac42d2f8b9ba0c2f0f2f2ec87ddbd529275fea5
对于项目的commit的id，git 是按照这个commit的git来对比submodule的变动的

这两个文件都需要提交到父项目的git中

git 只记录了submodule目录，不会记录目录下的文件
我们还可以这样用
 
$ git add .gitmodules pod-ibrary
$ git commit -m "pod-library submodule"
$ git submodule init

修改提交Submodule

首先要确认有对submodue的commit权限，
进入submodule目录里面，对修改的文件进行提交
cd pod-library/
我们修改了其中的一个文件看下文件的变动
git status

	modified:   pod-library/UseAFHTTP.h

commit submodule
git commit -a -m'test submodule'
push 到远端
git push

这个时候我们回到父目录：
cd ..
git status
n branch master


	modified:   pod-library (new commits)

可以看到pod-library已经变更为最新的commit id
Subproject commit 330417cf3fc1d2c42092b20506b0d296d90d0b5f
这个我们也需要推送到父项目的远端
git commit -m'update submodule'
git push


更新submodule
更新的方法有两种
一种是在父项目的目录下运行
git submodule foreach git pull

一种在submodule的目录下面更新
cd pod-library
git pull

注意更新submodule的时候如果有新的commit id产生，需要在父项目产生一个新的休息，pod-libray文件中的 Subproject commit会变为最新的commit id

clone的时候初始化Submodule
采用递归参数 --recursive
git clone git@github.com:jjz/pod-project.git --recursive

loning into 'pod-project'...
remote: Counting objects: 57, done.
remote: Compressing objects: 100% (45/45), done.
remote: Total 57 (delta 13), reused 49 (delta 8), pack-reused 0
Receiving objects: 100% (57/57), 18.79 KiB | 0 bytes/s, done.
Resolving deltas: 100% (13/13), done.
Checking connectivity... done.
Submodule 'pod-library' (git@github.com:jjz/pod-library.git) registered for path 'pod-library'
Cloning into 'pod-library'...
remote: Counting objects: 34, done.
remote: Compressing objects: 100% (25/25), done.
remote: Total 34 (delta 8), reused 30 (delta 7), pack-reused 0
Receiving objects: 100% (34/34), 12.95 KiB | 0 bytes/s, done.
Resolving deltas: 100% (8/8), done.
Checking connectivity... done.
Submodule path 'pod-library': checked out '330417cf3fc1d2c42092b20506b0d296d90d0b5f'
会自动clone submodule 

先clone父项目
git clone git@github.com:jjz/pod-project.git
cd pod-project
git submodule init
Submodule 'pod-library' (git@github.com:jjz/pod-library.git) registered for path 'pod-library'
git submodule update
Cloning into 'pod-library'...
remote: Counting objects: 34, done.
remote: Compressing objects: 100% (25/25), done.
remote: Total 34 (delta 8), reused 30 (delta 7), pack-reused 0
Receiving objects: 100% (34/34), 12.95 KiB | 0 bytes/s, done.
Resolving deltas: 100% (8/8), done.
Checking connectivity... done.
Submodule path 'pod-library': checked out '330417cf3fc1d2c42092b20506b0d296d90d0b5f'

删除submodle
git 并不支持直接删除submodle需要手动删除对应的文件
cd pod-project
git rm --cached pod-library
rm -rf pod-library
rm .gitmodules

 vim .git/config
  [submodule "pod-library"]
      url = git@github.com:jjz/pod-library.git
   删除submodule相关的内容
 git commit -a -m 'remove pod-library submodule'
 




