#  Meteor：发布与订阅
我们可以使用安全的方法让用户端不直接操作数据库，但是还是可以直接读取数据库内容，如果我们还需要保护私有的数据存储,在客户端直接使用`Collection.find()`，这样的操作方式在实际的项目中并不会使用，这样的数据无法保证私有性和安全性。如果一些数据我们并不想自动的在客户端更新，就需要自定义`publish`和`subscribe`，如何在*Meteor*中自定义发布与订阅模式呢?

## Remove Autopublish
在 [Meteor:方法控制](https://segmentfault.com/a/1190000004404874)中我们移除了 `insecure`，在这里我们需要移除`autopublish`。顾名思义，autopublish的意思就是meteor自动的实现数据的publish/subscribe。
>meteor remove autopublish

移除之后在我们添加数据以后，页面就不再直接显示数据了。想要显示数据，我们需要使用`Meteor.publish`和`Meteor.subscribe`方法，让服务端通知客户端的数据更新。
## 发布与订阅
## # publish
```
if (Meteor.isServer) {
    Meteor.publish("languages", function () {
        return Languages.find();
    });
}

```
## # subsribe
```
if (Meteor.isClient) {
    Meteor.subscribe("languages");

    Template.meteor_collection.helpers({
        languages: Languages.find({}, {sort: {createdAt: -1}})
    });
 });
```
在Server端使用`Meteor.publish`函数注册一个"languages"的发布者，需要在客户端对"languages"进行订阅，使用`Meteor.subscribe`订阅了"languages",现在已经添加的数据就会重新出现在页面上。
利用发布/订阅模式，我们也可以实现对于私有数据的访问。
## 给数据添加private
在页面上添加一个private的按钮:
```
<template name="other_event">
    {{# each others}}
        <li>
            <h3>{{name}} ,{{updateAt}}</h3>
            <button class="delete">delete</button>
            <button class="update">update</button>
            {{# unless private}}
                <button class="private">private</button>
            {{/unless}}
        </li>

    {{/each}}
</template>

```
添加设置为privte的方法：
```
Meteor.methods({

    addLanguage: function (text) {
        Languages.insert({
            name: text,
            createdAt: new Date()
        });


    },
    updateLanguage: function (_id) {
        Languages.update(_id, {
            $set: {updateAt: new Date()}
        })

    },
    removeLanguage: function (_id) {
        Languages.remove(_id);
    },
    setPrivate: function (_id) {
        Languages.update(_id, {
            $set: {private: true}
        })
    }
});
```
客户端调用设置privte的方法：
```
Template.other_event.events({
        'click .delete': function () {
            Meteor.call("removeLanguage", this._id);

        },
        'click .update': function () {
            Meteor.call("updateLanguage", this._id);
        },
        'click .private': function () {
            Meteor.call("setPrivate",this._id);

        }

    });
```
## 设置发布的数据必须是私有的
在Meteor.publish的可以使用filter过滤掉不是改用户的，非私有的数据：
```
  Meteor.publish("languages", function () {
        return Languages.find({
            $or: [
                {private: true}
                //auth user
                //,{owner:this.userId()}
            ]
        });

    });
```
测试这样的数据可以使用不通的用户在不通的浏览器上面做测试，每个用户都是只能看到自己的数据。
## 对删除数据的操作做检查
只允许用户对于自己的数据进行删除操作
```
 removeLanguage: function (_id) {
        var language=Languages.findOne(_id);
        if(language.private&&language.owner !=Meteor.userId){
					throw new Meteor.Error("not-authorized");
        }
        Languages.remove(_id);
    }
```
这样我们就能保证私有数据的安全，客户端不能直接操作数据（remove insecure）,客户端也不直接访问数据库(remove autopublish)，就有效的保护私有数据的安全。

项目地址 ：https://github.com/jjz/meteor/tree/master/meteor-publish



