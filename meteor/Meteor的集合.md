#Meteor的集合:Collections的使用
##Collections是什么
`Collections`是Meteor存储数据的方式，数据库使用的是`MongoDB`。
`Collections`允许访问客户端和服务端，会自动更新，当`Collections`发生改变的时候，所有使用`Collections`的组件都会被更新。使用`Collections`不需要编写大量的服务器代码就可以方便的更新页面逻辑。
客户端调用`Collections`和服务器调用`Collections`基本一致。
 `Collections`使用MongoDB实现,每一个`Collections`对应MongoDB中的一个表。
##创建一个Collections
创建`Collections`很简单,直接new一个Colletcion即可：
>Languages=new Mongo.Collection('languages');

这样就可以使用`find()`查询数据了,使用`Collections`查询数据的客户端代码:
```
    if (Meteor.isClient) {
      Template.meteor_collection.helpers({
        languages:Languages.find()
      });
    }
```
在模板中显示查询到的数据:
```
    <template name="meteor_collection">
      {{#each languages}}
          <h3>{{name}}</h3>
      {{/each}}
    </template>
```
因为还没有插入数据，所以这个使用页面中并没有数据可以显示，下面插入一些数据。
##使用MongoDB插入数据
进入项目目录中，确保你的`MongoDB`服务在运行中，如果没有运行，可以启动Mongo服务，也可以启动项目,启动项目时会自动启动`MongoDB`服务。
然后运行命令：
>meteor mongo

```
    ongoDB shell version: 2.6.7
    connecting to: 127.0.0.1:3001/meteor
    meteor:PRIMARY>
  ```

>show tables;

并没有看到数据显示，下面插入一条数据：
>db.languages.insert({name:'nodejs',created_time:new Date()})

再来看下:
>show tables;

```
    languages
    system.indexes
```
多了两个表 一个是`languages`一个是`索引表`。
在页面里面就可以看到一条数据。
##在server端添加数据
上面的查询是在客户端使用`Collections`,下面演示下在服务器端使用`Collections`的方法，在Meteor启动的时候判断如果数据小于2条就新加一条数据。

```
    if (Meteor.isServer) {
      Meteor.startup(function () {
        if(Languages.find().count()==1){
            Languages.insert({name:"css",created_time:new Date()});
        }
      });
    }
```

这样就在服务器端添加了一条数据，直接就可以在客户端查看到数据变化。
`Meteor`的`Collections`的特点:在客户端和服务端使用相同的数据模型，一次定义之后两端使用，`Meteor`会完成剩下的数据同步的工作。

项目地址：https://github.com/jjz/meteor/tree/master/meteor-collection

