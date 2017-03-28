#npm升级所有的依赖包
使用npm管理node的包，可以使用`npm update <name>`对单个包升级，对于npm的版本大于 `2.6.1`,可以使用命令:
>npm install -g

升级全局的本地包。
 
对于版本小于`2.6.1`的一个一个包的升级实在是太麻烦，就想找到一个升级所有本地包的方法，找到两个比较好的方式:`shell脚本`和`npm-ckeck`

## shell脚本
使用shell脚本升级npm包，首先所在找到需要升级的包和版本号，再使用`npm install`完成升级。
`npm -g`是管理本地全局包的命令。通过`npm -g outdated`可以查看那些包有更新：
>npm -g outdated 

```
Package    Current  Wanted  Latest  Location
appium       1.5.2   1.5.3   1.5.3
bower        1.7.0   1.7.9   1.7.9
cordova      5.4.1   6.2.0   6.2.0
eslint      2.13.0   3.0.0   3.0.0
fsevents     1.0.8  1.0.12  1.0.12
grommet      0.4.1   0.6.9   0.6.9
requirejs   2.1.22   2.2.0   2.2.0
```
这里列出来了，当前版本，和最后的版本，只需要得到所有需要升级的包名和版本号就可以使用`npm -g install <name>`直接升级了。
`npm -g outdated`还可以使用目录的方式展示，再从中提取出包名和版本号。
>npm -g outdated --parseable --depth=0

```
/usr/local/lib/node_modules/appium:appium@1.5.3:appium@1.5.2:appium@1.5.3
...
```
在通过`cut`命令就可以得到最后要升级版本号和包名：
>npm -g outdated --parseable --depth=0 | cut -d: -f2

```
appium@1.5.3
.....
```

完整的脚本：
```
#!/bin/sh
set -e
#set -x
for package in $(npm -g outdated --parseable --depth=0 | cut -d: -f2)
do
    npm -g install "$package"
done
```
脚本下载地址：[https://github.com/jjz/script/blob/master/npm-upgrade.sh](https://github.com/jjz/script/blob/master/npm-upgrade.sh)
## npm-check

[npm-check](https://www.npmjs.com/package/npm-check)是用来检查npm依赖包是否有更新，错误以及不在使用的，我们也可以使用npm-check进行包的更新。
安装npm-check：
>npm install -g npm-check

检查npm包的状态:
>npm-check -u -g

![CA5E1D6E-93B8-40CA-B190-273B87364C8C.png](http://upload-images.jianshu.io/upload_images/22188-aef0b264869c5366.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

通过上下键可以移动光标，使用空格键可以选择需要处理的包，回车直接进行处理。
选择`npm@3.10.2`包升级到`3.10.3`：

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

通过以上两种方式可以更便利的管理本地的`npm`包。

参考：https://gist.github.com/othiym23/4ac31155da23962afd0e

