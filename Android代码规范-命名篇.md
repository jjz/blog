#Android代码规范-命名篇
编码规范对于程序员尤为重要，可以有效的帮助我们进行`code review`，提高代码的可读性，让其他人更快更彻底的理解代码。
一个软件的生命周期中，80%的花费都在于维护以及新功能的迭代，很多的时候都需要阅读代码，读自己以前写的代码，读别人写的代码，这样规范的代码优势就显示出来了，符合规范的代码可以让人简单快速的理解理解代码的意图。
代码规范先从命名规范开始，Android的命名规范主要涉及：Java源代码，xml文件，图片资源。
##包名
首先说下Android包名的命名规则。
Andorid的包名一般采用域名的反转，单词全小写。域名为`www.example.com`的包名为`com.example`,省略**www**。
包名的开始是一个顶级域名，比如*com*,*cn*,*org*等，包名使用`.`做为分隔符。第二位一般是二级域名，也可以根据不同机构各自的命名。
后面的命名可以用部门，项目等进行区分（也可以没有）,例如:
>com.example.project

在项目内可以根据功能不同，按照模块划分不同的包名,`com.example.project.user`就是表示用户模块。
也可以根据层级的不同而划分不同的包名,比如:`com.example.prokect.activity`,就是Acitivity相关的包。
当然也可以在不同层级里面再按照模块划分包名，比如:`com.example.project.activity.user`，表示和用户有关的**Activity**。
总结,包名一般是以反转域名开始，后面跟有项目名称(缩写，也可以没有)。
后面可以采用的区分包名方式:

1. 按照模块 `com.example.project.user`
2. 按照层级区分 `com.example.project.activity`
3. 层级下也可以在区分模块 `com.example.project.activity.user`

##类和接口
类名是一个或多个单词组成，采用大驼峰命名，尽量要使类名简洁且利于描述,例如:`SignInActivity`，类名规则如下：
> * 大驼峰命名
> * 简洁而富有表达性
> * 尽量不使用缩写（广泛使用的单词除外，比如URL,XML...）
> * 多单词中采用 `名词+动词`的方式命名: `LocationManage`
> * 对于缩写单词要全部大写比如:`XMLManage`

一个类如果继承了Android的组件，需在使用该组件的名称作为后缀，这样容易区分该类的作用，比如:`SgnInActivity`,`UserInfoFragment`,`FileUploadService`...
接口一般使用****I****开头，采用大驼峰命名规则，比如：`IPullToRefresh`。
##变量
Android变量分为三种：成员变量,静态变量和常量。

* ####成员变量

成员变量采用小驼峰命名规则，第一单词的首字母小写，其后的首字母大写。变量名一般不使用_和$开头。例如：
>private Intent cropIntent;

变量名应简短且易于描述，选用规则尽量简单，易于记忆和联想。
尽量避免单个字符的变量名，除非是用于一次性的临时变量,临时的整形变量一般命名为 i,j,k,m,n。字符型的变量一般使用c,d,e。
对于`View`变量的命名规则，如果`View`是一个单词的，采用第一个单词小写的方式+对应View的描述进行,例如：
> private View viewUserInfo;

如果是两个单词组成的View，比如:`TextView`,一般采用缩写的方式,例如:
> private TextView tvUserName;
一般情况下`Button`缩写为:btn。

* ####静态变量

为了可以很方便的区分静态变量，静态变量的命名一般采用小写的s开头，后面单词的命名规则和`成员变量`保持一致，例如:
>private static Map<String, String> sCacheStrings;

* ####常量

常量命名规则一般是所有的单词都是大写，中间使用_（下划线）分割，例如：
>private static final float SCALE_RATE = 1.25f;

代码中不允许出现单独的字符串或数字常量，比如`xx.equals("1")`,单独的字符串或数字不利于后期的维护。如果需要使用数据或字符，请按照他们的含义封装成静态常量，或者使用枚举，for语句除外。

##方法
方法命名规则采用小驼峰命名法例如:onCreate(),onRun()，方法名一般采用动词或者动名词。
一般使用的方法名前缀。
> * getXX()返回某个值的方法
> * initXX() 初始化相关方法，比如初始化布局:initView()
> * checkXX()和isXX() 方法为boolean值的时候使用is或者check为前缀
> * saveXX() 保存数据
> * clearXX()和removeXX() 清除数据
> * updateXX() 更新数据
> * processXX() 对数据进行处理
> * dispalyXX() 显示某某信息
> * draswXX() 绘制数据或者效果

另外对于方法的其他一些规范：

* 方法的参数尽可能不超过4个，需要更多的参数的时候可以采用class的方法
* 方法参数中尽量少使用boolean,使用boolean传参不利于代码的阅读
* 方法尽量不超过15行，方法过长，说明当前方法业务逻辑过于复杂，需要进行方法拆分
* 一个方法只做一件事，
* 如果一个方法返回的是一个错误码，可以使用异常
* 不使用try catch 处理业务逻辑
* 尽可能不实用null，替代为异常或者使用空的变量，比如`Collections.emptyList()`

##Layout
Layout的命名规则需要和使用他们的组件对应，方便查找和维护，比如我们在创建一个用户信息的`UserInfoActivity`，对应的Layout的命名就应该是`activity_user_info.xml`。
对应Andorid组件的`Layout`命名规则:

* Activity  ->   `activity_user_info.xml`
* Fragment  ->   `fragment_sign_up.xml`
* Dialog    ->   `dialog_change_password.xml`
* AdapterView Item  ->  `item_user.xml`
* Layout只是一部分  ->    `partial_stats_bar.xml`

##string和color
项目中使用的string,和color的值原则上都是必须放在`strings.xml`和`colors.xml`中，不要放在Java代码中，这样的好处是可复用，提高维护性，减少非必要的代码。
`xml`的资源命名,字母全部小写，多个单词之间使用_(下划线)分割.
比如：
><string name="app_name">example</string>

建议color的命名中体现其`ARGB`值，比如:
><color name="color_feb749">#feb749</color>
这样的写法对于代码提示更加的友好,有利于对照标注图查找颜色值。

##*id*命名
layout中使用的`id`的单词要全部小写，单词之间使用下划线分割，使用名词或者名词词组，应该通过id的命名可以直接理解当前的`View`要实现的功能.
例如：
>@+id/tv_user_name_show

id命名的第一个单词使用View的缩写，如果View只是一个单词，缩写就是当前单词。一般`Button`的缩写为：**btn**。

##Drawable命名
Drawable的命名规则根据使用的控件来命名，控件的缩写在前面，后面使用表示其功能的一个或者多个单词，中间使用使用_下划线分割。

* **Action bar**使用`ab_`，比如:`ab_stacked.png`
* **Button** 使用`btn_`
* **Dialgo** 使用`dialog_` 
* **Divide** 使用 `divider_` 
* **Icon** 使用 `ic_`
* **Menu** 使用`menu_`
* **Notification**使用 `notification_`
* **Tabs** 使用`tab_`

Drawable是有多个状态的，在命名中体现出状态的不同：

* **Normal**  对应`_normal`结尾，比如`btn_order_normal.9.png`
* **Pressed** 对应`_pressed`结尾
* **Focused** 对应`_focused`结尾
* **Disabled** 对应`_disabled`结尾
* **Selected** 对应`_selected`结尾

其他资源文件的命名需要遵守Android的规范即可，比如`arrays.xml`数组文件，`dimens.xml`分辨的配置，`style.xml`样式的配置，资源文件的ID命名规则都是才是字母小写，使用下划线分割的原则。



