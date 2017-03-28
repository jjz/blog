# Meteor:方法控制
在之前的例子中，我都是使用的很小的demo,所有的测试都可以在客户端直接对database进行CURD.实际的项目中有可以需要对于用户操作databse有一定的权限控制，在meteor中，最好的方法是通过自定义方法(`call method`)的形式，代替用户端直接进行，insert,update和remove。通过这种方式我们可以用权限验证的方式对于用户端提交的行为判断，确定用户的提交形式是符合我们的要求的。
## 移除 insecure
每个meteor创建的项目，默认是 `insecure`的 ，默认是允许用户在客户端是可以更改database的，现在需要移除`insecure`
>meteor remove insecure
```
Changes to your project's package version selections:

insecure  removed from your project

insecure: removed dependency
```

`insecure`成功移除，移除之后我们在*Meteor.isClient*中的insert,update,remove都无法使用了，因为不开启insecure的应用不能在客户端直接更新database的数据,需要使用下面的方法才可以更新database。
## 定义方法
我们定义方法让客户端代码可以操作数据库，`Meteor.methods`用来定义方法的，他定义的方法可以不需要在*client*或者*server*里面，两边都可以访问！

```
Meteor.methods({
    addLanguage: function (text) {
        //   auth
        //if (!Meteor.userId()) {
        //    throw new Meteor.Error("need login");
        //}

        Languages.insert({
            name: text,
            createdAt: new Date()
        });


    }
});
```
再对update 和remove操作增加定义的方法：

```
updateLanguage: function (_id) {
        Languages.update(_id, {
            $set: {updateAt: new Date()}
        })

    },
    removeLanguage: function (_id) {
        Languages.remove(_id);
    }
```

## 调用 Meteor.Methods
调用已经定义的Meteor需要使用：`Meteor.call`

```
  Template.body.events({
        'submit .new-language': function (event) {
            event.preventDefault();
            var text = event.target.text.value;
            Meteor.call("addLanguage", text);

        }
    });
```
`Meteor.call`的第一个参数是函数名字，第二个参数是需要传的变量。同样的我们需要增加update和delete方法。

现在insert，update和delete又可以正常的在工作了。
这种工作方式的原理类似与ajax，客户端发送请求到服务器端，服务器更新database，通知客户端，客户端正确的显示数据。


项目地址：https://github.com/jjz/meteor/tree/master/meteor-security


