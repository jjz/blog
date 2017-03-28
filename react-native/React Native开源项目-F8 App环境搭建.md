#React Native开源项目-F8 App环境搭建

Facebook在2016年的F8大会上开源了F8 App，F8 App是使用React Native开发的,毫无疑问这是一个特别棒的React Native的学习模板。项目地址：https://github.com/fbsamples/f8app
运行起来F8 App需要React Native,Redux,Relay,GraphQL等。
## 环境要求

* Node
* React Native :IOS和Android的配置环境。[React Native环境搭建](https://segmentfault.com/a/1190000004477951)
* MongoDB  [Mac上安装MongoDB](https://segmentfault.com/a/1190000003692872)
如果你想运行IOS的Demo的话，还需要：
* Xcode 7.3+
* CocoaPods:ios项目的包管理工具
运行Android项目的话，需要：
* Gradle
*Android SDK

## 初始化项目
下载源代码:
```
git clone git@github.com:fbsamples/f8app.git
cd f8app
```
安装项目依赖:
```
npm install
#ios 依赖 
cd ios
pod update
#android依赖
cd ../android
gradle build
```
## 使用测试数据
启动MongoDB:
> mongod --config /usr/local/etc/mongod.conf

测试MongoDB是否启动成功：
>mongo

出现：
```
MongoDB shell version: 3.2.6
>
```
就是MongoDB启动成功了。
启动Parse/GraphQL的服务：
>npm start

导入测试数据:
>npm run import-data
确认下面的地址是可以访问的，并且是有数据的。

* Parse Dashboard: http://localhost:8080/dashboard

![28AC51C6-87D2-4163-94F8-B3618E527162.png](http://upload-images.jianshu.io/upload_images/22188-f406aeb0738de92c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* Graph/QL: http://localhost:8080/graphql
![E2C5BE07-E5E7-4501-BF5A-9A3F66F47AE7.png](http://upload-images.jianshu.io/upload_images/22188-3a2339818b93e556.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 运行
运行ios:
>react-native run-ios

运行android:
>react-native run-android

运行效果图：
![2BCD4C9A-23D1-4667-B40D-00431974090B.png](http://upload-images.jianshu.io/upload_images/22188-53a7ca9d0b17b215.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

更多的学习在：http://makeitopen.com/

