#beego的ORM：配置数据库
`Object Relational Mapping `,简称ORM，用于实现面向对象编程语言里不通类型系统的数据之间的转换。从效果上说，它其实是创建了一个可在编程语言里使用的“虚拟对象数据库”。
beego是谢大神开发的一个快速开发Go应用的Http框架，一个RESTful的框架。
记录下如何在beego中配置数据库，以`MySql`为例
##数据库选择
目前beego的ORM支持三中数据库:
	1.Sqlite
	2.PostgreSql
	3.MySql

如果要使用数据库必须把对应的`drive`加入import中:
```
import (
    _ "github.com/go-sql-driver/mysql"
    _ "github.com/lib/pq"
    _ "github.com/mattn/go-sqlite3"
)
```
##注册数据库
>orm.RegisterDriver("mysql", orm.DRMySQL)


这句话的注册了mysql的数据库,当然我们要import进来对于的orm包:
```
import(
"github.com/astaxie/beego/orm"
)
```
第一个参数是driverName,第二个参数是orm的类型对于三种数据库:
```
orm.DRMySQL
orm.DRSqlite
orm.DRPostgres
```

##连接数据库
beego必须注册一个别名为`default`的数据库，作为默认使用。
>orm.RegisterDataBase("default", "mysql", "test:123456@/test?charset=utf8",30,30)

第一个参数是数据库的别名，用来切换数据库使用。
第二个是`driverName`，在RegisterDriver时注册的
第三是数据库连接字符串:`test:123456@/test?charset=utf8`相对于`用户名:密码@数据库地址+名称?字符集`
第三个参数和第四个参数也可以不传值，会使用数据库默认值：
第三个参数相当于:
>orm.SetMaxIdleConns("default", 30)
设置数据库的最大空闲连接。
第四个参数相当于：
>orm.SetMaxOpenConns("default", 30)
设置数据库的最大数据库连接。

##时区
曾经我遇到过一个项目，数据库的时区设置的ORM的时区不一致，导致后面的开发会复杂很多。
beego默认使用`time.Local`本地时区，可以用来自动创建时间，从数据中取出时间转换。
>orm.DefaultTimeLoc = time.UTC
可以设置默认的时区。
在进行RegisterDataBase的时候，会获取数据库使用的时区，做响应的转换，匹配时间，从而保证时间不会出错。


