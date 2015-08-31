开始学习使用MongoDB

MongoDB是什么

MongoDB是一个基于分布式文件存储的数据库。由C++语言编写。旨在为WEB应用提供可扩展的高性能数据存储解决方案。
他的特点:高性能、易部署、易使用，存储数据非常方便

在mac 上面安装MongoDB
你可以通过编译源代码来安装
一般在mac上面我们都是使用强大的Homebrew安装盒管理软件。
首先更新Homebrew的package数据库

brew update 

更新完成之后，直接安装MongoDB

brew install mongodb

==> Downloading https://homebrew.bintray.com/bottles/mongodb-3.0.6.yosemite.bottle.tar.gz
Already downloaded: /Library/Caches/Homebrew/mongodb-3.0.6.yosemite.bottle.tar.gz
==> Pouring mongodb-3.0.6.yosemite.bottle.tar.gz
==> Caveats
To have launchd start mongodb at login:
  ln -sfv /usr/local/opt/mongodb/*.plist ~/Library/LaunchAgents
Then to load mongodb now:
  launchctl load ~/Library/LaunchAgents/homebrew.mxcl.mongodb.plist
Or, if you don't want/need launchctl, you can just run:
  mongod --config /usr/local/etc/mongod.conf
==> Summary
🍺  /usr/local/Cellar/mongodb/3.0.6: 17 files, 159M

启动MongoDB 
上面最后的提示的是直接启动Mongodb的方法
mongod --config /usr/local/etc/mongod.conf

mongod.conf中是关于MongoDB的设置
systemLog://log 目录
  destination: file
  path: /usr/local/var/log/mongodb/mongo.log
  logAppend: true
storage://db目录
  dbPath: /usr/local/var/mongodb
net://网络地址
  bindIp: 127.0.0.1
连接到MongoDB 
mongo

测试下
>db.test.insert({'name':'test'})
WriteResult({ "nInserted" : 1 })
> db.test.find()
{ "_id" : ObjectId("55e407e120d5b7acf4301d3b"), "name" : "test" }
>







