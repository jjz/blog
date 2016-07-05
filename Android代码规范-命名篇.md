#Android代码规范-命名篇
编码规范对于程序员尤为重要，可以有效的帮助我们`code review`，提高代码的可读性，可以让其他开发者更快更彻底的理解新的代码。一个软件的生命周期中，80%的花费都在于维护，很多的时候我们都需要读代码，读自己以前写的代码，读别人写的代码，这样代码的规范的优势就显示出来了。
这篇内容主要是关于命名规范的。
##包名
Andorid的包名一般采用域名的反转表示:`com.example`，就是包名的开始是一个顶级域名，比如com,cn,org等，包名都是使用`.`做为分隔符的。
第二位一般是二级域名，也可以根据不同机构各自的命名不尽相同。
后面的命名可以用来区分部门，项目等进行区分（也可以不没有）,例如:
>com.example.project
在项目内会根据功能的不模块划分不同的包名,`com.example.project.user`就是用户项目的模块。
也可以根据层级的不同而花费不同的包名,`com.example.prokect.activity`，当然我们也可以在不同层级里面在按照模块划分报名:`com.example.project.activity.user`。
报名一般是以反转域名开始`com.example`，后面跟有项目名称(缩写，也可以没有)。在后面可以区分包名的方式:

1. 按照模块 `com.example.project.user`
2. 按照层级区分 `com.example.project.activity`
3. 层级下也可以再按照模块 `com.example.project.activity.user`

##类和接口
类名是一个或多个单词组成，采用大小写混合的方式，尽量要使类名简洁且利于描述。
类名采用大驼峰命名的方式，首字母大写,例如:`SignInActivity`，类名规则如下：
> * 大驼峰命名
> * 简洁而富有表达性
> * 多个单词中的每个单词的首字母都大写
> * 尽量不使用缩写（广泛使用的单词除外，比如URL,XML...）
> * 多单词中采用 `名词+动词`的方式命名: `LocationManage`
> * 对于缩写单词要全部大写比如:`XMLManage`

一个如果继承了Android的组件，需在使用该组件的名称作为后缀，比如:`SgnInActivity`,`UserInfoFragment`,`FileUploadService`...
接口一般使用**I**开头，采用大驼峰命名规则，比如：`IPullToRefresh`。
##变量
变量分为三中成员变量,静态变量，常量。
>####成员变量

第一单词的首字母小写，其后的首字母大写。变量名一般不使用_和$开头。例如：
>private Intent cropIntent;

变量名应简短且易于描述，变量名的选用尽量简单，易于记忆，易用编译器的自动提示，尽量避免单个字符的变量名，除非用于一次性的临时变量,临时的整形变量一般命名为 i,j,k,m和n。字符型的变量一般使用c,d,e。
对于`View`在变量中的命名方式，如果`View`是一个单词采用第一个单词小写的方式+View的描述,例如：
> private View viewUserInfo;

如果是两个单词组成的View，比如:`TextView`,一般采用缩写的方式,例如:
> private TextView tvUserName;

>####静态变量

为了可以很方便的区分静态变量，静态变量的命名一般采用小写的s开头，后面单词的命名规则和`成员变量`保持一致，例如:
>private static Map<String, String> sCacheStrings;

>####常量
常量一般所有的单词都是大写，中间使用_（下划线）分割，例如：
>private static final float SCALE_RATE = 1.25f;


代码中不允许出现单独的数字，或者字符比如xx.equals("1"),如果需要使用数据或字符，请按照他们的含义封装成静态常量。for语句除外。


##方法
采用小驼峰命名法例如:onCreate(),onRun()，方法名一般采用动词或者动名词，一般可能会需要的方法前缀。
> * getXX()返回某个值的方法
> * initXX() 初始化相关方法，比如初始化布局:initView()
> * checkXX()和isXX() 方法为boolean值的时候使用is或者check为前缀
> * saveXX() 保存数据
> * clearXX()和removeXX() 清除数据
> * updateXX() 更新数据
> * processXX() 对数据进行处理
> * dispalyXX() 显示某某信息
> * draswXX() 绘制数据或者效果

另外对于方法的一些其他一些的规范:

* 方法的参数尽可能不超过4个，需要更多的参数的时候可以采用class的方法
* 方法参数中尽量少使用boolean,使用boolean传参不利于代码的阅读
* 方法尽量不超过15行，方法过长，说明当前方法业务逻辑过于复杂，需要进行方法拆分
* 一个方法只做一件事，
* 如果一个方法返回的是一个错误码，可以使用异常
* 不使用try catch 处理业务逻辑
* 尽可能不实用nuull，替代为异常或者使用空的变量，比如`Collections.emptyList()`


##Layout
Layout的命名需要和使用他们的组件对应，比如我们在创建一个用户信息的`UserInfoActivity`，对应的Layout的命名就应该是`activity_user_info.xml`。
对应Andorid组件的命名规则:
> Activity  ->   `activity_user_info.xml`
> Fragment  ->   `fragment_sign_up.xml`
> Dialog    ->   `dialog_change_password.xml`
> AdapterView Item  ->  `item_user.xml`
> 部分Layout ->    `partial_stats_bar.xml`
##string和color
项目中使用的string,和color的值原则上都是必须放在`strings.xml`,或者`colors.xml`，不要放在Java代码中。
xml的资源命名必须才是字母全部小写，多个单词之间使用_(下划线)分割.
比如：
><string name="app_name">example</string>

建议color的命名中体现其`ARGB`值，比如:
><color name="color_feb749">#feb749</color>
这样的写法对于代码提示更加的友好

##id命名
layout中使用的`id`必须全部小写，单词之间使用下划线分割，使用名词或者名词词组，通过id可以直接理解当前组建要实现的功能.
例如：
>@+id/user_name_show
##Drawable命名
根据控件使用的命名，控件的缩写在前面，后面使用表示其功能的一个或者多个单词，中间使用使用_下划线分割。

* **Action bar**使用`ab_`，比如:`ab_stacked.png`
* **Button** 使用`btn_`
* **Dialgo** 使用`dialog_` 
* **Divide** 使用 `divider_` 
* **Icon** 使用 `ic_`
* **Menu** 使用`menu_`
* **Notification**使用 `notification_`
* **Tabs** 使用`tab_`

*selector*是有多个状态的，在命名中就需要体现出对于的状态不同：

* Normal  对应`_normal`结尾，比如`btn_order_normal.9.png`
* Pressed 对应`_pressed`结尾
* Focused 对应`_focused`结尾
* Disabled 对应`_disabled`结尾
* Selected 对应`_selected`结尾



