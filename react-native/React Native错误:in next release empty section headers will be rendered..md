# React Native错误:in next release empty section headers will be rendered.
升级`React Native`到最新版本出现了一个`Warning`：
```
Warning:In next release empty section headers will be rendered.
In this release you can use 'enableEmptySections' flag to render empty section headers
```

这个错误出现在`ListView`中，在以后的版本中才会实现空的`section headers`作为默认值，当前版本并没有支持。
如果我们不需要使用headers的话，可以禁止**EmptySections**。
解决方法：
```
 <ListView
      style={styles.listView}
      dataSource={this.state.dataSource}
      enableEmptySections={true}
      />
```
参考文档:
  [http://facebook.github.io/react-native/docs/listview.html# enableemptysections](http://facebook.github.io/react-native/docs/listview.html# enableemptysections)

