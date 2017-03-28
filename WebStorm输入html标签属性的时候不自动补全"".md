# WebStorm输入html标签属性的时候不自动补全""

在WebStrom输入属性之后回车，出现=和""
```
 return (
        <Image
               source=""
               style={styles.page}/>
        );
```

但是在react开发中或者是支持jsx语法的开发中，常常需要使用`style={styles.page}`这个的语法，并不是输入字符串。这样就有个超级不方便的问题，每次都需要删除""。
关闭""的自动提示：
![图片描述][1]
在Preferences->Editor->General->Smart Keys->Add quotes for attribute value on typing '=' and attribute completion
可以关闭""的自动补全:
```
 <Image
     source=
     style={styles.page}/>
```


  [1]: /img/bVteuj













