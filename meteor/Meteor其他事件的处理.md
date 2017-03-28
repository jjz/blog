#  Meteor其他事件的处理
除了表单提交以外，还有我们日常用到的CRUD操作，对object操作的四个方法: *Read/Insert/Update/Delete*
Read这个在一开始的例子中就有说到的:`db.object.find()`,通过`Tempalte.tempaltes.helpers`给变量赋值，在html中使用模板语言对变量进行操作。
Insert在之前的from表单提交中介绍的就是Insert操作。

```
 Languages.insert({
                name: text,
                createdAt: new Date()
         });
```

下面来说下其他的两个操作`Update`和`Delete`
## 增加Update和Delete的Button
先增加一个template名字叫做other_event,在这个模板中我们只展示3个objects，给每天object添加一个删除和更新的按钮。

```
<template name="other_event">
    {{# each others}}
        <li>
            <h3>{{name}} ,{{updateAt}}</h3>
            <button class="delete">delete</button>
            <button class="update">update</button>

        </li>

    {{/each}}
</template>

```

在之前的表中并没有updateAt这个字段，这里使用了mongodb（nosql）的一个优势，灵活可变的数据模式(scheme)，不需要变更表结构就可以随意的增加字段。
之前没有updateAt数据的字段在{{updateAt}}这个位置会显示为"".

## Update操作

```
 Template.other_event.events({
       'click .update': function () {
            Languages.update(this._id, {
                $set: {updateAt: new Date()}
            })
        }

    });
```
这里给class为`update`的`button`添加一个事件，更新这个object的updateAt为当前时间。
这里的this就是一个obejct对象，`_id`是Mongodb的自动的对每个插入的文档对象做的唯一个一个索引，Mongodb为保证其*唯一*和*自增长*。
使用`$set`可以给language对象赋值要更新的属性.
同样的我们可以打开两个浏览器，在其中的一个浏览器中点击更新按钮，在两个浏览器中都可以看到数据的更新。
   >css ,Fri Dec 18 2015 09:40:26 GMT+0800 (CST)
   
   ## Delete操作
     ```
   Template.other_event.events({
        'click .delete': function () {
            Languages.remove(this._id);

        };
    });
     ```
通过主键*_id*对language对象进行删除操作。同样的我们也可以字两个浏览器中进行观察数据的更新。

项目地址：https://github.com/jjz/meteor/tree/master/meteor-form


