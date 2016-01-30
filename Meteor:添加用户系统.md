# Meteor:添加用户系统
Meteor 内部实现了对用户系统的支持，可以让你的应用程序分分钟种支持多用户系统。
要开启对于Accounts 系统和UI的支持，我们需要添加相关的packages,cd 到App的目录，添加相关的包:
>meteor add accounts-ui
meteor add accounts-password

一个是用户系统相关的ui包，一个是password相关的包
##添加login按钮
直接在body里面添加login的模板即可:
>{{> loginButtons}}

效果如下：
![图片描述][1]

点击按钮会弹出一个登录框：
![图片描述][2]

可以登录，也可以跳转到注册界面

##在取用户信息
在html中可以通过`currentUser`这个变量获取到用户的信息

```
{{#if currentUser}}
    <div>
        <strong>{{currentUser.username}}</strong>
    </div>
    {{> add}}
    {{> detail}}
{{/if}}
```

`currentUser`可以判断用户是否登录,没有登录的用户该值为空,如果用户已经登录可以通过`currentUser.username`得到用户的名字。

##数据和用户信息关联
使用add的template添加一条language数据
页面：
```
<template name="add">
    <form class="new-language">
        <input type="text" name="text" placeholder="add new language">
        <input type="submit" value="Submit"/>
    </form>
</template>
```
添加事件：
```
Template.add.events({
        "submit .new-language": function (event) {
            event.preventDefault();
            console.log("test");
            console.log(Meteor.user().username);
            var text = event.target.text.value;
            Languages.insert({
                text: text,
                createdAt: new Date(),
                owner: Meteor.userId(),
                username: Meteor.user().username
            });


        }
    });
    ```

在保存Lanuages的时候，额外保存了两条用户相关的数据 一个是用户的id，一个是用户的名称。owner保存的是user的 _id就是MongoDB的主键。通过`Meteor.userId()` 可以获得当前登录用户的id，`Meteor.user()`可以获得当前登录的用户，Meteor.user()在JavaScript中使用，而currentUser在html中使用。
   
##在数据中显示用户信息
在lanuage信息中，显示出用户名
```
<template name="detail">
    {{#each languages}}
        <div class="text"><strong>{{username}}</strong> -{{text}}</div>
    {{/each}}
</template>
```

获取数据的代码：
```
Template.detail.helpers({
        languages: Languages.find({owner: Meteor.userId()})
   });
```

从保存到数据库中的用户信息中，显示用户的名称。

项目源代码地址：https://github.com/jjz/meteor/tree/master/meteor-account

  [1]: /img/bVsAwF
  [2]: /img/bVsAwG

