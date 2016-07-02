#npm升级所有的依赖包
使用npm管理nodejs的包，可以使用`npm update <name>`升级当个的包，当时没有命令能够当前所有的依赖包。一个一个包查找升级又会太麻烦，就想找到一个升级所有本地依赖包的方法。

##shell脚本
`npm -g`是管理本地全局包的命令。通过`npm -g outdated`可以查看那些包有更新：
>npm -g outdated 

```
Package    Current  Wanted  Latest  Location
appium       1.5.2   1.5.3   1.5.3
bower        1.7.0   1.7.9   1.7.9
cordova      5.4.1   6.2.0   6.2.0
eslint      2.13.0   3.0.0   3.0.0
fsevents     1.0.8  1.0.12  1.0.12
grommet      0.4.1   0.6.9   0.6.9
requirejs   2.1.22   2.2.0   2.2.0
```
只需要得到所有升级的包名和版本号就可以使用`npm -g install <name>`直接升级。
`npm -g outdated`还可以使用目录的方式展示。
>npm -g outdated --parseable --depth=0

```
/usr/local/lib/node_modules/appium:appium@1.5.3:appium@1.5.2:appium@1.5.3
...
```
在通过`cut`命令就可以得到最后要升级版本号和包名：
>npm -g outdated --parseable --depth=0 |cut -d: -f2
```
appium@1.5.3
...
```
完整脚本：
```
#!/bin/sh
set -e
set -x
for package in $(npm -g outdated --parseable --depth=0 | cut -d: -f2)
do
    npm -g install "$package"
done
```
脚本地址：[https://github.com/jjz/script/blob/master/npm-upgrade.sh](https://github.com/jjz/script/blob/master/npm-upgrade.sh)
##npm-check

[npm-check](https://www.npmjs.com/package/npm-check)是用来检查npm依赖包是否过期,错误，以及不在使用的包。
安装npm-check：
>npm install -g npm-check

检查npm包的状态:
>npm-check -u -g

通过上下键可以移动光标，使用空格键可以选择需要处理的包
选择`npm@3.10.2`包升级到`3.10.3`

```
? Choose which packages to update. npm@3.10.3

$ npm install --global npm@3.10.3 --color=always
/usr/local/bin/npm -> /usr/local/lib/node_modules/npm/bin/npm-cli.js
/usr/local/lib
└─┬ npm@3.10.3
  ├── aproba@1.0.4
  ├── has-unicode@2.0.1
  └── read-package-tree@5.1.5

[npm-check] Update complete!
[npm-check] npm@3.10.3
[npm-check] You should re-run your tests to make sure everything works with the updates.
```

通过以上两种方式可以更方便的管理已经存在本地的`npm`包。

参考：https://gist.github.com/othiym23/4ac31155da23962afd0e


