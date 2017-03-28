#  AAPT: Error retrieving parent for item: No resource found that matches the given name 'android:Widget.Material.CompoundButton.RadioButton'

项目中要使用appcompat-v7的包，从ADT22.6.0版本之后，有了这个库，这是Google自己的一个兼容包，能让2.1以上全使用上4.0的界面，例如使用ActionBar等，不再需要使用第三方的支出库了。
添加依赖：
>compile 'com.android.support:appcompat-v7:23.1.1'

添加了23.1.1版本的appcompat之后，出现了一些错误
```
/app/build/intermediates/res/merged/etcp/debug/values-v21/values-v21.xml:176 : AAPT: Error retrieving parent for item: No resource found that matches the given name 'android:Widget.Material.CompoundButton.RadioButton'.

/Users/jjz/Documents/.../app/build/intermediates/res/merged/.../debug/values-v21/values-v21.xml:177 : AAPT: Error retrieving parent for item: No resource found that matches the given name 'android:Widget.Material.DropDownItem.Spinner'.

/Users/jjz/Documents/.../app/build/intermediates/res/merged/.../debug/values-v21/values-v21.xml:181 : AAPT: Error retrieving parent for item: No resource found that matches the given name 'android:Widget.Material.ImageButton'.
....
```

从API 19之后才增加的`appcompact_v7`,appcompact_v7会从你的SDK中引用资源内容。这里的报错是因为没有找到v21的内容，如果要编译appcompact_v7,最好使用最新的SDK API.后头再看下我的compileSdkVersion是19。
需要把appcompact_v7升级为最新的23。
> compileSdkVersion 23









