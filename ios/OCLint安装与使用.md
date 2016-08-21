`OCLint`是一个强大的静态代码分析工具，可以用来提高代码质量，查找潜在的bug，主要针对c,c++和Objective-c的静态分析。功能非常强大，而且是出自国人之手。项目地址：http://oclint.org/。

主要能检查到的代码缺陷: 可能出现的bug，空的if/else/try/catch/finally的参数 没有使用的变量或者参数 复杂的代码逻辑，多个if/else的判断 不需要的代码 过长的方法或者参数 错误的分配方式 ....

##安装OCLint OCLint是一个开源的项目，你可以通过[源码](https://github.com/oclint/oclint)安装，需要设置对应的环境变量，也可以使用作者发布的[release](https://github.com/oclint/oclint/releases)进行安装，不过我更习惯使用`Homebrew`安装。 首先需要设置brew的第三方仓库`oclint/formulae`。 >brew tap oclint/formulae 然后安装`OCLint`。 > brew install oclint

针对对OCLint升级的方法: >brew update >brew upgrade oclint

使用`brew cleanup`可以清理旧版本的安装数据。 ##xcodebuild`xcodebuild`是xcode的编译命令。 可以查看xcodebuild的版本信息: >xcodebuild -version

```
Xcode 7.3.1
Build version 7D1014
```

也可以查看当前系统的sdk以及其版本： >xcodebuild -showsdks

```
OS X SDKs:
	OS X 10.11                    	-sdk macosx10.11

iOS SDKs:
	iOS 9.3                       	-sdk iphoneos9.3

	...
```

需要使用xcodebuild [flags]命令进行编译并把相关的日志信息输入到xcodebuild.log中，需要在`.xcodeproj`文件所在的目录里面运行。 >xcodebuild | tee xocdebuild.log flags参数默认使用Release，这样就得到了一份编译代码信息相关的日志。 下面需要对日志使用`OCLint`进行分析

##分析日志 我们需要使用OCLint对日志信息进行分析运行命令 >oclint-xcodebuild xcodebuild.log

得到的信息:

```
This binary is no longer under maintenance by OCLint team.
Please consider using xcpretty (https://github.com/supermarin/xcpretty) instead!
```

oclint-xcodebuild不在使用了，需要安装[xcpretty](https://github.com/supermarin/xcpretty),使用xcpretty命令分析日志信息。`xcpretty`是用来格式化`xcodebuild`输出的工具，使用ruby开发。 安装: >gem install xcpretty

使用参数`--report json-compilation-database`或者`-r json-compilation-database`可以生成指定生成数据的格式，当前指定为json格式。 运行命令： >xcodebuild |xcpretty -r json-compilation-database 在`build/reports`中得到文件`compilation_db.json`

##分析json数据 对于json数据的分析需要使用`oclint-json-compilation-database`。 运行命令 >oclint-json-compilation-database -- -o=report.html

这样可以到一个html的报告。 但是运行的运行的时候却得到了一个错误信息：

```
Error: compile_commands.json not found at current location.
```

找不到complie_commands.json文件，使用`xcpretty`生成的文件名是:compilation_db.json,在目录`build/reports`目录中。 因此需要把`xcpretty`生成的文件`compilation_db.json`复制到当前目录下。重命名为`compile_commands.json`。 再次运行命令得到生成结果：report.html。 ##oclint分析json参数 开发者会在项目中会大量使用第三方库，在做`OCLint`需要忽略对于第三方库的检查，在运行`oclint-json-comilation-database`的时候可以使用**-e**忽略不需要分析的目录比如: >oclint-json-compilation-database -e Pods -- -o=report.html

还可以改变一些检查的方式，比如`OCLint`有一个方法数的检查`Long method P3 Method with 179 lines exceeds limit of 100`，方法数超过100行就会出现这个警告，可以通过参数改变默认100行的限制。 >oclint-json-compilation-database -e Pods -- -rc=LONG_LINE=200 -o=report.html

##脚本化 根据上面的步骤写了一个shell脚本用来执行OCLint的检查

```
#! /bin/sh
if which oclint 2>/dev/null; then
    echo 'oclint exist'
else
    brew tap oclint/formulae
    brew install oclint
fi
if which xcpretty 2>/dev/null; then
    echo 'xcpretty exist'
else
    gem install xcpretty
fi
cd test
xcodebuild clean
xcodebuild | xcpretty -r json-compilation-database
cp build/reports/compilation_db.json compile_commands.json
oclint-json-compilation-database -e Pods   -- -rc=LONG_LINE=200 -rc=NCSS_METHOD=100  -o=report.html

```
