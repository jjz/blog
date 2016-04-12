#使用JSPatch平台热修复iosApp

IOS产品开发中常常会遇到这种情况，线上发现一个严重bug,可能是一个crash,可能是一个功能无法使用，这时能做的只是赶紧修复Bug然后提交等待漫长的审核，即使申请加急也不会快到那里去，即使审核完了之后，还要盼望着用户快点升级，用户不升级还是在存在同样的漏洞，这样的情况让开发者付出了很大的成本才能完成Bug的修复。

JSPath就是为了解决这样的问题而出现的，只需要在项目中引入极小的JSPatch引擎，就可以还用JavaScript语言调用Objective-C的原生API，动态更新APP，修复BUG。

JSPaht本身是开源项目,项目地址:http://jspatch.com/,github地址: https://github.com/bang590/JSPatch
在他的官网上面给出了一个例子:
```
@implementation JPTableViewController
...
- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath
{
  NSString *content = self.dataSource[[indexPath row]];  //可能会超出数组范围导致crash
  JPViewController *ctrl = [[JPViewController alloc] initWithContent:content];
  [self.navigationController pushViewController:ctrl];
}
...
@end
```
可以通过下发下面的JavaScript代码修复这个bug:
```
//JS
defineClass("JPTableViewController", {
  //instance method definitions
  tableView_didSelectRowAtIndexPath: function(tableView, indexPath) {
    var row = indexPath.row()
    if (self.dataSource().length > row) {  //加上判断越界的逻辑
      var content = self.dataArr()[row];
      var ctrl = JPViewController.alloc().initWithContent(content);
      self.navigationController().pushViewController(ctrl);
    }
  }
}, {})

```

JSPtch需要一个后台服务用来下发和管理脚本，并需要处理传输安全等。

##注册获取AppKey
在平台上面注册一个账户，新建一个App可以拿到对应的AppKey。
##导入SDK到项目中
SDK地址:http://jspatch.com/Index/sdk
当前下载下来的SDK版本名称是:`JSPatch 2.framework`,需要去掉中间的空格，不然导入项目的时候会报错。
导入项目的时候要选择`Copy items if needed`。
还需要添加对于的依赖框架`JavaScriptCore.framework`和`libz.tbd`.
##添加JSPatch代码
在AppDelegate.m中添加代码:
```
#import "AppDelegate.h"
#import <JSPatch/JSPatch.h>

@implementation AppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    [JSPatch startWithAppKey:@"f78378d77e5783e8"];
    [JSPatch sync];
    return YES;
}
@end
```

##在平台中上传js修复文件
为了简单我们只上传一个简单的`UIAlertView`,弹出一个提示框：
```
ar alertView = require('UIAlertView').alloc().init();
alertView.setTitle('Alert');
alertView.setMessage('AlertView from js');
alertView.addButtonWithTitle('OK');
alertView.show();

```
用JavaScript实例化了UIAlertView，文件名需要命名为`main.js`。
##从服务器下发到客户端
把`main.js`上传到服务器上，下发到版本为1.0的客户端上面。
在请求服务加载脚本的时候出现了一个错误：`The resource could not be loaded because the App Transport Security policy requires the use of a secure connection.`
这个错误出现的原因是ios9引入了新特性`App Transport Security(ATS)`,简单来说就是APP内部的请求必须使用HTTPS协议。
很明显这里的url并没有使用https，我们可以通过设置先规避掉这个问题:
	1.在info.plist中添加NSAppTransportSecurity类型为Dictionary.
	2.在NSAppTransportSecurity中添加NSAllowsArbitraryLoads类型为Boolean，值为YES
![图片描述][1]

运行效果：
![图片描述][2]


  [1]: /img/bVuOP8
  [2]: /img/bVuOQ0

