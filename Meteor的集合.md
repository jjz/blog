# Meteor的集合
##Collections是什么
Collections是Meteor存储数据的方式，数据库使用的是`MongoDB`。Collections允许客户端和服务端访问，他们会自动更新，所有支持的组件都会显示最新的。使用Collections不用编写大量的服务器代码就可以很容易的编写页面逻辑。客户调用Collections和服务器调用Collections基本一致。
每一个Collections对应MongoDB中的一个表。
##创建一个Collections
创建一个Collections很简单
>Languages=new Mongo.Collection('languages');
可以使用Languages.find()查询所需数据
使用Collections查询数据的客户端代码:
    if (Meteor.isClient) {
      Template.meteor_collection.helpers({
        languages:Languages.find()
      });
    }

在模板中显示数据:
    <template name="meteor_collection">
      {{#each languages}}
          <h3>{{name}}</h3>
      {{/each}}
    </template>
这个使用页面中并没有数据可以显示，下面插入一些数据。
##使用MongoDB插入数据
进入项目目录中，确保你的MongoDB服务在运行中，如果没有运行，你可以收到启动Mongo服务，也可以启动你的项目来启动MongoDB服务。
然后运行命令：
>meteor mongo

    ongoDB shell version: 2.6.7
    connecting to: 127.0.0.1:3001/meteor
    meteor:PRIMARY>
    
>show tables;

并没有任何数据显示，
插入一条数据：
>db.languages.insert({name:'nodejs',created_time:new Date()})
再来看下:
>show tables;
    languages
    system.indexes
多了两个表 一个languages一个是索引表。
在页面里面就会显示一条数据
##在server端添加数据
在Meteor启动的时候判断如果数据小于2条就新加一条数据。
    if (Meteor.isServer) {
      Meteor.startup(function () {
        if(Languages.find().count()==1){
            Languages.insert({name:"css",created_time:new Date()});
        }
      });
    }

Meteor的Collections的特点就是在客户端和服务端使用相同的数据模型，一次定义两边使用。Meteor完成剩下的数据同步更新的工作。

项目地址：https://github.com/jjz/meteor/tree/master/meteor-collection


