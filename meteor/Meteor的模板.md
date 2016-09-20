新建一个项目`Meteor`
>meteor create meteor-template

会生成三个文件：
```
meteor-template.css
meteor-template.html
meteor-template.js
```
这三个文件就是模板文件,`.js`文件是逻辑和数据控制文件,`.css`文件是样式文件。
访问`.html`文件会自动加载`.js`和`.css`文件。

##在HTML文件中使用Meteor模板

`Meteor模板`定义三个最高级别的标签`<head>` `<body>` `<template>`
```
    <head>
          <title>meteor-study</title>
    </head>
    
    <body>
        <h1>I am Meteor!</h1>
        {{> meteor_study}}
    </body>
    <template name="meteor_study">
        <h2>{{templateName}}</h2>
        {{#each languages}}
            <h3>{{name}}</h3>
        {{/each}}
    </template>
```
不用写`<!DOCTYPE html>`了，能省则省!
`Meteor`的模板可以自由的使用html标签比如上面的使用的`<h1>`标签。

##在模板中使用逻辑
`{{}}`是模板语言，是安全的不会输出`html`标签
他具有以下常用的语法:

* `{{> meteor_study}}`导入指定的模板
* `{{#each langages}} {{/each}}`循环
* `{{#if done}}done{{else}}notdone{{/if}}` if判断
* 等...
详细的说明可以[参考](https://github.com/meteor/meteor/blob/devel/packages/spacebars/README.md)。

##给模板中的变量赋值
在`MVC`的模式中可以在Controller层给View层赋值，在`Meteor`中也可以在`.js`文件中给模板赋值:
```
    if (Meteor.isClient) {
      Template.meteor_study.helpers({
          templateName:'Meteor Study',
          languages:[{name:'Node'},{name:'Meteor'},{name:'html'},{name:'css'}]
    
      });
    }
```
其中`Meteor.isClient`这句话判断是不是在客户端，在客户端就使用`Template.meteor_study.helpers` 给模板变量赋值。使用`templateName`和`languages`分别定义了一个字符串和一个数组。
##使用css
`.css`文件负责控制样式,在meteor-template.css添加代码:
```
    body {
        font-family: sans-serif;
        background-color: #315481;
        background-image: linear-gradient(to bottom, #315481, #918e82 100%);
        background-attachment: fixed;
    
        position: absolute;
        top: 10px;
        bottom: 0;
        left: 10px;
        right: 0;
    
        padding: 0;
        margin: 0;
    
        font-size: 14px;
    }
    
    
    header {
        background: #d2edf4;
        background-image: linear-gradient(to bottom, #d0edf5, #e1e5f0 100%);
        padding: 20px 15px 15px 15px;
        position: relative;
    }
    
    h1 {
        font-size: 1.5em;
        margin: 0;
        margin-bottom: 10px;
        display: inline-block;
        margin-right: 1em;
    }
```
最终显示的效果:

![显示效果](http://upload-images.jianshu.io/upload_images/22188-143f40ee84d794fa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
代码地址：https://github.com/jjz/meteor/tree/master

