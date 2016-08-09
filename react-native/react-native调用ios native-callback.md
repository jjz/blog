#react-native调用ios native方法-Callback

上一篇简单介绍了使用[react-native调用ios native方法](http://www.jianshu.com/p/e8d2d8e1e21f),在真实的使用场景中，不仅仅只是调用Native的方法就可以了，还需要对结果进行处理，Native处理完之后返回结果，在JavaScript中进行处理。
这样就需要使用JavaSctipt的回调，对结果进行处理。在React Native中有两种方式的回调:`RCTReponseSenderBlock`和`Promises`。
##RCTReponseSenderBlock
在定义可供JavaScript调用的Native的时候，有一类参数叫做`RCTReponseSenderBlock`。
`RCTReponseSenderBlock`是在`RCTBridgeModule.h`定义的block.完整的定义:
```
typedef void (^RCTResponseSenderBlock)(NSArray *response);
```
`RCTReponseSenderBlock`定义个一个Bridge的操作，返回给JavaScript一个callback的方法。他定义的参数是一个`NSArray`。其中第一个参数是`error`代表着错误信息，如果没有错误传入null。后面的参数传入自定义的内容。
具体实例，给`UIAlertView`添加两个按钮，在点击按钮之后使用回调返回JavaScript。
首先需要实现`UIAlertViewDelegate`：
```
@interface RNIOSAlert : NSObject<RCTBridgeModule,UIAlertViewDelegate>
@end
```
然后定义一个变量用来保存`RCTReponseSenderBlock`的参数。
```
@implementation RNIOSAlert{
  RCTResponseSenderBlock _alertCallback;
}
@end
```
在`clickedButtonAtIndex`中处理结果并调用回调函数:
```

-(void)alertView:(UIAlertView *)alertView clickedButtonAtIndex:(NSInteger)buttonIndex{
  if (buttonIndex==0) {
    _alertCallback(@[@"cancel",]);
  }else{
    _alertCallback(@[[NSNull null],@1]);
  }
}
```
最后定义带有回调参数的Native方法:
```

RCT_EXPORT_METHOD(showAlertAndCallback:(RCTResponseSenderBlock)callback){
  _alertCallback=callback;
  UIAlertView * alertView=[[UIAlertView alloc] initWithTitle:@"react-native" message:@"是否继续？" delegate:self cancelButtonTitle:@"关闭" otherButtonTitles:@"继续", nil];
  [alertView show];

  
}
```
在JavaScript中使用:
```
 _alertCallback() {

        RNIOSAlert.showAlertAndCallback(function (err, datas) {
            if (err) {
                console.warn('err', '已取消');
            } else {
                console.warn('data', '请继续');
            }

        });
    }
```
每次显示`UIAlertView`之后都可以看到处理的结果。

##Promises
Promises是ES6中的特性，统一的为JavaScript提供异步编程的接口，避免Callback地狱，解决了Callback的层层嵌套。更加容易的对异步操作进行控制。
在React Native中对Promises有很完善的支持，调用Object-C 的Native方法的时候，也可以Promise的方式让代码执行从Object-C 回到JavaScript中。
> * Resolve和Reject
`Resolve`和`Reject`分量是Promise的两种状态，已解决和已拒绝，`Resolve`是正常的执行结果，而`Reject`会触发`catch`操作。
在Object-C与之相对应的是：`RCTPromiseResolveBlock`和`RCTPromiseRejectBlock`，两都是定义好的Object-C bridge，对应Promise的两种状态。
`RCTPromiseResolveBlock`的实现：
```
typedef void (^RCTPromiseResolveBlock)(id result);
```
已`id`为参数，当然必须是Object-c和JavaScript定义好的参数。
`RCTPromiseRejectBlock`的实现：
```
typedef void (^RCTPromiseRejectBlock)(NSString *code, NSString *message, NSError *error);
```
这里使用了NSError，可以自己传入code以及message。
> * 使用Promise实现回调
首先要定义两个变量:
```
@implementation RNIOSAlert{
  RCTPromiseResolveBlock _resolveBlock;
  RCTPromiseRejectBlock _rejectBlock;
}
```
在定义个提供给JavaScript调用的函数:
```
RCT_REMAP_METHOD(alertUserPromise, resolver:(RCTPromiseResolveBlock)resolver rejecter:(RCTPromiseRejectBlock)reject){
  _resolveBlock=resolver;
  _rejectBlock=reject;
  UIAlertView * alertView=[[UIAlertView alloc] initWithTitle:@"react-native" message:@"使用Promise？" delegate:self cancelButtonTitle:@"关闭" otherButtonTitles:@"继续", nil];
  [alertView show];
}
```
这里使用`RCT_REMAP_METHOD`，他的第一个参数是方法名，后面的参数是实现的方法，在JavaScript调用Promise并不会在方法名中体现。
处理结果并回调:
```

-(void)alertView:(UIAlertView *)alertView clickedButtonAtIndex:(NSInteger)buttonIndex{
  if (buttonIndex==0) {
    NSError * err=[NSError errorWithDomain:@"test" code:0 userInfo:nil];
    _rejectBlock(@"0",@"cancel",err);
  }else{
    _resolveBlock(@[@1]);
  }

}

```
JavaScript中调用：
```
 _alertUsePromise() {
        RNIOSAlert.alertUserPromise().then((datas)=> {
            console.warn('data', datas);
        }).catch((err)=> {
            console.warn('err', err);
        });
    }
```
也可以使用async/await实现
```
 async  _alertPromise() {
        try {
            var datas = await RNIOSAlert.alertUserPromise();
            console.warn('data', datas);
        } catch (e) {
            console.warn('err', e);
        }
    }

```
这里使用的是async/await关键词，这两个关键词把Promises的思想融入到语言本身。这要就不需要写catch这样的伪同步的代码，可以使用try/catch/return这样的关键词了。
Promises对于回调的使用很方便，尽量避免JavaScript的回调地狱。

代码地址：https://github.com/jjz/react-native/tree/master/RNIos


