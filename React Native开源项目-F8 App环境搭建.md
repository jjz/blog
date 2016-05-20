#React Native开源项目-F8 App环境搭建

Facebook在2016年的F8大会上开源了F8 App，毫无疑问这是一个特别棒的React Native的学习模板。运行起来F8 App需要React Native,Redux,Relay,GraphQL等。
##环境要求
React Native :IOS和Android的配置环境。
MongoDB 
当然如果你想运行IOS的Demo的话，还需要
Xcode 7.3+
CocoaPods:ios项目的包管理工具

##初始化项目
下载源代码:
```
git clone git@github.com:fbsamples/f8app.git
cd f8app
```
安装依赖:
```
npm install
#ios 依赖 
cd ios
pod update
```
##导入测试数据
启动MongoDB:
> mongod --config /usr/local/etc/mongod.conf

测试MongoDB是否启动成功：
>mongo
出现
```
MongoDB shell version: 3.2.6
```
启动MongoDB成功。
启动Parse/GraphQL的服务：
>npm start

导入测试数据:
>npm run import-data
确认下面的地址是可以访问的，是有数据的。

* Parse Dashboard: http://localhost:8080/dashboard
* Graph/QL: http://localhost:8080/graphql


##运行
ios:
>react-native run-ios
android:
>react-native run-android


