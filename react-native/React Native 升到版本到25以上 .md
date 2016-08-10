#React Native 升到版本到25以上

React Native的更新策略设定的是两个星期一个迭代,使用旧版本的React Natvie没有多长时间就需要升到到最新版本。

React Native 24之后，一个显著的变化是import *React*的时候不再从`react-native`中导入，而是从`react`中导出。
##使用React
第一步要使用`React`的依赖，如果把react-natie的版本直接升高到当前版本，比如:`"react-native": "0.31.0"`。再使用`npm install ` 安装依赖包的时候，会得到提示:
```
npm WARN react-native@0.31.0 requires a peer of react@~15.2.1 but none was installed.
```
就需要我们添加对于`React`的依赖,可以才是命令直接添加依赖：
>npm install -save react@~15.2.1

也可以在`package.json`中直接添加依赖:
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
在运行`npm install`就可以了。

##import React from 'react'
从React Native 25开始，出了React不再从`react-native`中引入之外，还包括Component,PropTypes,Children等...
升级完对应的依赖之后，直接运行项目之后还是会得到一个报错信息:
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
提示我们需要把Component等从包`react`中引入,当前这样需要修改所有的问题。我们从[更新说明](https://github.com/facebook/react-native/releases/tag/v0.25.1)中可以看到可以使用工具来做。
[codemod-RN24-to-RN25](https://github.com/sibelius/codemod-RN24-to-RN25)是一个升级React Nateive 25之前的版本的工具，用来更改成正确的`import`。
使用方法:
 
 1. 安装jscodeshift  
 	>npm install -g jscodeshift
 
 2. clone项目,
  >git clone git@github.com:sibeliusseraphini/codemod-RN24-to-RN25.git
 
 3. copy  transform.js
   > cd codemod-RN24-to-RN25
   > cp transform.js `YOUR_PROJECT_PATH`
  
 4. 运行命令
    > cd `YOUR_PROJECT_PATH`
    > jscodeshift transform.js
可以看到`React`以及`Component`被正确的引用到了`react`包下。


