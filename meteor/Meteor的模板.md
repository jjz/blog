# Meteor的模板使用Meteor新建项目
>meteor create meteor-template
会产生三个文件：
	meteor-template.css
	meteor-template.html
	meteor-template.js
这三个文件html就是模板文件,js是逻辑和数据控制文件,css就是样式。
访问.html文件的时候会自动加载其他两个文件。


##在HTML文件中使用Meteor模板

Meteor模板定义三个最高级别的标签`<head>` `<body>` `<template>`

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

不用写`<!DOCTYPE html>`了，能省则省。
Meteor的模板里面可以自由的使用html标签比如上面的`<h1>`

##在模板中使用逻辑

`{{}}`是模板语言，是安全的不会输出html
`{{> meteor_study}}`导入指定的模板

`{{#each langages}} {{/each}}`循环
`{{#if done}}done{{else}}notdone{{/if}}` if判断
等...
详细的说明可以看 https://github.com/meteor/meteor/blob/devel/packages/spacebars/README.md

##给模板中的变量赋值

    if (Meteor.isClient) {
      Template.meteor_study.helpers({
          templateName:'Meteor Study',
          languages:[{name:'Node'},{name:'Meteor'},{name:'html'},{name:'css'}]
    
      });
    }

`Meteor.isClient`这句话判断是不是在客户端，在客户端使用`Template.meteor_study.helpers` 给模板变量赋值,templateName和languages分布定义了一个字符串和一个数组。
##使用css
在meteor-template.css

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

最终效果:
![图片描述][1]

代码地址：https://github.com/jjz/meteor/tree/master


  [1]: /img/bVp1VC

