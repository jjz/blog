#  Attribute "title" has already been defined
在添加android-support-v7-appcompat之后出现了一个错误
>error: Attribute "title" has already been defined

搜索了一下在项目的attrs.xml中，的确有 有个title的定义
```
<attr name="title" format="string" />
```
declare-styleable是给自定义控件添加自定义属性用的，目前在项目中这个属性并没有使用，把自定义的*title*删除
重新编译通过


