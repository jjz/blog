# React Native 升级到版本到0.25.1

React Native的更新策略是两个星期迭代一个新版本,使用的React Natvie没有多长时间就需要升到最新版本了，这里介绍下如何升级到版本0.25.1以及其以上。

React Native 25，一个显著的变化是*import React*的时候不再从`react-native`中导入，而是从`react`中导入。所以首先需要添加`react`的依赖。
## 使用React
首先要添加`React`的依赖，如果把react-natie的版本直接升高到当前版本，比如:`"react-native": "0.31.0"`。
使用`npm install ` 安装依赖包的时候，会提示错误:
```
npm WARN react-native@0.31.0 requires a peer of react@~15.2.1 but none was installed.
```
就需要我们添加对于`React`的依赖,可以使用命令添加`React`的依赖：
>npm install -save react@~15.2.1

也可以在`package.json`中添加依赖:
```
{
  "name": "rn310",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "node node_modules/react-native/local-cli/cli.js start"
  },
  "dependencies": {
    "react": "^15.2.1",
    "react-native": "0.31.0"
  }
}
```
在运行`npm install`就可以自动下载依赖了。

## import React from 'react'
React Native 25之后，除了React不再从`react-native`中引入之外，还包括Component,PropTypes,Children等...
这里要注意,旧版本的React是从`react-native`中import的。
如果升级完依赖之后，直接运行项目之后会得到一个报错信息:
```
Seems you're trying to access 'ReactNative.Component' from the 'react-native' pakeage.
Perhaps you meant to access 'React.Component' from the 'React' package instead?
For example, instead of :

import React, { Component, View } from 'react-native';
you should now:

import React, { Component } from 'react';
import { View } from 'react-native';
.....
```

![import React](http://upload-images.jianshu.io/upload_images/22188-92f0fcbb799b462a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

提示的内容就是告诉我们需要把Component,React等从包`react`中引入,不能再从`react-native`中引入。
如果要更新引入，基本上要修改所有的文件，还好我们从[更新说明](https://github.com/facebook/react-native/releases/tag/v0.25.1)中可以看到有工具来做这个事情。
[codemod-RN24-to-RN25](https://github.com/sibelius/codemod-RN24-to-RN25)是一个升级项目文件支持`React Native 25`的工具，更改文件的`import`，让需要引入`react`的文件能够正确的`import`。
使用方法:
 
 1. 安装`jscodeshift`  
 	>npm install -g jscodeshift
 
 2. clone项目,
  >git clone git@github.com:sibeliusseraphini/codemod-RN24-to-RN25.git
 
 3. copy  *transform.js*
   > cd codemod-RN24-to-RN25
   > cp transform.js `YOUR_PROJECT_PATH`
  
 4. 运行命令转换文件
    > cd `YOUR_PROJECT_PATH`
    > jscodeshift transform.js

等待运行完成之后可以看到`React`以及`Component`等都能被正确的引用到`react`包下。


