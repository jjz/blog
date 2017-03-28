#  Meteor的表单提交:form
## Meteor中的form
在web中时时刻刻都不能离开form，表单提交,来看看在meteor里面如何提交表单吧！
首先是 HTML:

  

```
  <head>
    <title>meteor-form</title>
</head>

<body>
<h2>form</h2>
<div class="container">
    <form class="new-language">
        <input type="text" name="text" placeholder="add language"/>
        <input type="submit" value="Submit"/>
    </form>
</div>
<h2>Welcome to Meteor!</h2>

{{> meteor_collection }}

</body>

<template name="meteor_collection">
    {{# each languages}}
        <h3>{{name}}</h3>
    {{/each}}
</template>
```

这里我们定义了一个表单，class为new-lanuage，有一个text的 input还有一个提交的按钮。下面的模板`meteor_collection`是用来显示的。
## 给表单添加事件

```
if (Meteor.isClient) {

    Template.meteor_collection.helpers({
        languages: Languages.find({}, {sort: {createdAt: -1}})
    });

    Template.body.events({
        'submit .new-language': function (event) {
            event.preventDefault()
            var text = event.target.text.value
            console.log(event)
            Languages.insert({
                name: text,
                createdAt: new Date()
            })
            event.target.text.value = ""
        }
    });

}
```

使用`Template.templateName.events(...)`可以给模板添加事件 ，可以是`submit`,`click` ,`hover`...,function里面可以做相应的处理，这里使用.new-language是css的选择器，用来指定form.
`event`是事件提交的参数，包括事件提交的所有内容。
可以使用consloe.log(event)在浏览器中看到event的属性：

![图片描述][1]

## 对结果排序
在插入的时候，多加了一个字段createdAt，我们可以按照时间的倒序对 languages进行排序：
 >languages: Languages.find({}
    , {sort: {createdAt: -1}})
 
 使用`meteor run` 运行项目之后 ，可以打开两个浏览器，在其中一个浏览器中添加一个语言，就能在另一个浏览器中立刻看到。

项目地址：https://github.com/jjz/meteor/tree/master/meteor-form


  [1]: /img/bVrCNf


