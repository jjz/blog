#使用JSPatch热修复IOS App

IOS App常常会遇到这种情况，线上发现一个严重`bug`,可能是某一个地方`Crash`,也可能是一个功能无法使用，这时能做的只有赶紧修复Bug然后提交`app store`等待漫长的审核。 即使申请加急审核但是审核速度仍然不会快到那里去，即使审核完了之后，还要盼望着用户快点升级，用户不升级同样的漏洞一直存在，这种情况让开发者付出了很大的成本才能完成对于`Bug`的修复,有可能还需要出现强制升级的情况。

这样情况现在有办法改善，`JSPatch`就是为了解决这样的问题而出现的，只需要在项目中引入极小的一个`JSPatch`引擎，就可以使用`JavaScript`语言调用`Objective-C`的**原生API**，动态更新App，修复BUG。

`JSPatch`是一个开源的项目,项目网站:[http://jspatch.com/](http://jspatch.com/),Github地址: [https://github.com/bang590/JSPatch](https://github.com/bang590/JSPatch) 在`JSPatch`的官网上面给出了一个例子:

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

这里会出现一个数组越界的`Crash`可以通过下发下面的JavaScript代码修复这个Bug:

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

`JSPtch`需要一个后台服务用来下发和管理脚本，并需要处理传输安全等`JSPatch`平台提供了对应的服务。

## 注册获取AppKey 在`JSPatch`平台上面注册一个账户，新建一个App就可以拿到对应的AppKey。 ## 导入SDK到项目中 SDK地址:[http://jspatch.com/Index/sdk](http://jspatch.com/Index/sdk) 当前下载下的SDK版本名称是:`JSPatch 2.framework`,需要去掉中间的空格，不然导入项目的时候会报错。 导入项目的时候要选择`Copy items if needed`。 还需要添加对于的依赖框架`JavaScriptCore.framework`和`libz.tbd`. ## 添加JSPatch代码 在`AppDelegate.m`中添加代码:

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

## 在平台中上传`JavaScript`修复文件 为了简单我们只上传一个简单的`UIAlertView`,弹出一个提示框：

```
ar alertView = require('UIAlertView').alloc().init();
alertView.setTitle('Alert');
alertView.setMessage('AlertView from js');
alertView.addButtonWithTitle('OK');
alertView.show();

```

这段代码用`JavaScript`实例化了`UIAlertView`，文件名需要命名为`main.js`。 ## 从服务器下发到客户端 把`main.js`上传到服务器上，下发到版本为1.0的客户端上面。 在请求服务加载脚本的时候出现了一个错误：`The resource could not be loaded because the App Transport Security policy requires the use of a secure connection.` 这个错误出现的原因是ios9引入了新特性`App Transport Security(ATS)`,简单来说就是App内部的请求必须使用`HTTPS协议`。 很明显这里的url并没有使用https，我们可以通过设置先规避掉这个问题:

```
1. 在info.plist中添加NSAppTransportSecurity类型为Dictionary.
2. 在NSAppTransportSecurity中添加NSAllowsArbitraryLoads类型为Boolean，值为YES
```

![https](http://upload-images.jianshu.io/upload_images/22188-9a19b52eca5e1349.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

运行效果如下：

![运行效果](http://upload-images.jianshu.io/upload_images/22188-c5c9a5a7e30983dc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 这样就可以直接修复掉线上bug了，不需要等待`App Store`的审核。
