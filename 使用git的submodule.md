# 使用Git Submodule管理子模块
## 使用场景
基于公司的项目会越来越多，常常需要提取一个公共的类库提供给多个项目使用，但是这个`library`怎么和`git`在一起方便管理呢？
我们需要解决下面几个问题：

 - 如何在git项目中导入`library库`?
 - `library库`在其他的项目中被修改了可以更新到远程的代码库中?
 - 其他项目如何获取到`library库`最新的提交?
 - 如何在clone的时候能够自动导入`library库`?

解决以上问题，可以考虑使用git的 `Submodule`来解决。

## 什么是Submodule?
`git Submodule` 是一个很好的多项目使用共同类库的工具，他允许类库项目做为`repository`,子项目做为一个单独的`git项目`存在父项目中，子项目可以有自己的独立的`commit`，`push`，`pull`。而父项目以`Submodule`的形式包含子项目，父项目可以指定子项目`header`，父项目中会的提交信息包含`Submodule`的信息，再`clone父项目`的时候可以把`Submodule`初始化。


## 在项目中使用Submodule
使用`git`命令可以直接添加`Submodule`：
> git submodule add git@github.com:jjz/pod-library.git pod-library

使用 `git status`命令可以看到 
> git status
    
```
    On branch master
    Changes to be committed:
    
    	new file:   .gitmodules
    	new file:   pod-library
```
可以看到多了两个需要提交的文件：`.gitmodules`和 `pod-library` 
`.gitmodules` 内容包含`Submodule`的主要信息，指定`reposirory`,指定路径:

```
    [submodule "pod-library"]
    	path = pod-library
    	url = git@github.com:jjz/pod-library.git
```
可以看到记录了子项目的目录和子项目的`git`地址信息。
`pod-libray`内容只保护子项目的`commit id`，就能指定到对于的`git header`上,例如:
>Subproject commit 4ac42d2f8b9ba0c2f0f2f2ec87ddbd529275fea5

`4ac42d2f8b9ba0c2f0f2f2ec87ddbd529275fea5`就是子项目的`commit id`，父项目的git并不会记录`Submodule`的文件变动，它是按照`commit git`指定`Submodule`的`git header`。

另外,*这两个文件都需要提交到父项目的git中*。

还可以这样使用命令添加`Submodule`
```
git add .gitmodules pod-ibrary
git commit -m "pod-library submodule"
git submodule init
```
## 修改Submodule

**首先需要确认有对Submodule的commit权限**。
进入`Submodule`目录里面:
> cd pod-library/

修改其中的一个文件看下文件的可以用`git status`查看变动:
>    git status

```    
modified:   pod-library/UseAFHTTP.h
```
提交`Submodule`的更改内容：
 >git commit -a -m'test submodule'
 
然后`push` 到远程服务器:
 >git push

然后再回到父目录,提交`Submodule`在父项目中的变动：
>cd ..
git status
on branch master

```    
modified:   pod-library (new commits)
```
可以看到`pod-library`中已经变更为`Submodule`最新的`commit id`:
```
Subproject commit 330417cf3fc1d2c42092b20506b0d296d90d0b5f
```
需要把`Submodule`的变动信息推送到父项目的远程服务器
>git commit -m'update submodule'
   git push

这样就把子模块的变更信息以及子模块的变更信息提交到远程服务器了，从远程服务器上更新下来的内容就是最新提交的内容了。

## 更新Submodule
更新`Submodule`有两种方式:
 在父项目的目录下直接运行
>git submodule foreach git pull
  
在Submodule的目录下面更新
    >cd pod-library
    git pull

可以看到在`Submodule`的目录中,使用`git`和单独的一个项目是一样的,注意更新`Submodule`的时候如果有新的`commit id`产生，需要在父项目产生一个新的提交，pod-libray文件中的 `Subproject commit`会变为最新的`commit id`。

## clone Submodule
`clone Submodule`有两种方式 一种是采用递归的方式clone整个项目，一种是clone父项目，再更新子项目。
1. 采用递归参数 `--recursive`
> git clone git@github.com:jjz/pod-project.git --recursive

   输出结果：
```
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
    Submodule path 'pod-library': checked out '330417cf3fc1d2c
    
    42092b20506b0d296d90d0b5f'
```
可以看到`init Submodule` 会自动被`clone`下来

2. 第二种方法先clone父项目，再初始化`Submodule`
>git clone git@github.com:jjz/pod-project.git
cd pod-project
git submodule init

  输出:
```
    Submodule 'pod-library' (git@github.com:jjz/pod-library.git) 
    registered for path 'pod-library'
```
更新`Submodule`:
>git submodule update

  运行结果：
  ```
    Cloning into 'pod-library'...
    remote: Counting objects: 34, done.
    remote: Compressing objects: 100% (25/25), done.
    remote: Total 34 (delta 8), reused 30 (delta 7), pack-reused 0
    Receiving objects: 100% (34/34), 12.95 KiB | 0 bytes/s, done.
    Resolving deltas: 100% (8/8), done.
    Checking connectivity... done.
    Submodule path 'pod-library': checked out '330417cf3fc1d2c42092b20506b0d296d90d0b5f'
```
## 删除Submodule
`git` 并不支持直接删除`Submodule`需要手动删除对应的文件:
> cd pod-project
    git rm --cached pod-library
    rm -rf pod-library
    rm .gitmodules
    
更改git的配置文件`config`:
 > vim .git/config

可以看到`Submodule`的配置信息：
```
[submodule "pod-library"]
  url = git@github.com:jjz/pod-library.git
```      
 删除submodule相关的内容,然后提交到远程服务器:
 >git commit -a -m 'remove pod-library submodule'


代码地址:
父项目:https://github.com/jjz/pod-project
子项目:https://github.com/jjz/pod-library




