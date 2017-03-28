#react-native调用ios native方法-回调

上一篇中介绍了使用[react-native调用ios native方法](http://www.jianshu.com/p/e8d2d8e1e21f),在真实的使用场景中，不仅仅只是调用下Native的方法，还需要对结果进行处理，Native处理完之后返回结果再回调会JavaScript中进行操作和处理。
这样就需要使用JavaSctipt的回调函数，对结果进行处理。在React Native中Object-c有两种方式的回调:`RCTReponseSenderBlock`和`Promises`。
## RCTReponseSenderBlock
在JavaScript和Object-C的参数列表，有一类参数叫做`RCTReponseSenderBlock`对于JavaScript的**Function**，这个就是JavaScript调用Object-C的Callback(回调函数)。
> * `RCTReponseSenderBlock`是在`RCTBridgeModule.h`定义的block.
完整的定义:
```
typedef void (^RCTResponseSenderBlock)(NSArray *response);
```
`RCTReponseSenderBlock`定义个一个Object-C Bridge的操作，返回给JavaScript一个callback的方法。

他的参数是一个`NSArray`。其中第一个参数是`error`代表着错误信息，如果没有错误传入*null*,后面的参数传入自定义的内容。
具体实例:
先给`UIAlertView`添加两个按钮，在点击按钮之后使用`RCTResponseSenderBlock`返回JavaScript。
先实现`UIAlertViewDelegate`：
```
@interface RNIOSAlert : NSObject<RCTBridgeModule,UIAlertViewDelegate>
@end
```
定义一个变量用来保存参数:
```
@implementation RNIOSAlert{
  RCTResponseSenderBlock _alertCallback;
}
@end
```
在`clickedButtonAtIndex`方法中处理结果并调用回调函数:
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
最在JavaScript中调用Native方法，并处理回调:
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
每次关闭`UIAlertView`都可以看到JavaScript处理的结果。

## Promises
`Promises`是`ES6`中的特性，它的目的是统一为JavaScript提供异步编程的接口，避免Callback地狱，解决了Callback的层层嵌套。更加容易的对异步操作进行控制。
在React Native中对`Promises`有很完善的支持，调用Object-C 的Native方法的时候，也可以Promise的方式让代码执行从Object-C 回到JavaScript中。
先看Promises的两个状态。
>* Resolve和Reject
`Resolve`和`Reject`分量是Promise的两种状态，表示已解决和已拒绝，`Resolve`是正常的执行结果，而`Reject`会触发`catch`操作。
在Object-C与之相对应的是：`RCTPromiseResolveBlock`和`RCTPromiseRejectBlock`，两个都是定义好的Object-C bridge。

`RCTPromiseResolveBlock`的实现：
```
typedef void (^RCTPromiseResolveBlock)(id result);
```
以`id`为参数，当然传参必须是Object-c和JavaScript定义好的参数。
`RCTPromiseRejectBlock`的实现：
```
typedef void (^RCTPromiseRejectBlock)(NSString *code, NSString *message, NSError *error);
```
`RCTPromiseRejectBlock`使用了NSError，还可以传入自定义的*code*以及*message*。


 * 使用Promise实现回调
首先要定义两个变量用来保存参数:
```
@implementation RNIOSAlert{
  RCTPromiseResolveBlock _resolveBlock;
  RCTPromiseRejectBlock _rejectBlock;
}
```
在定义提供给JavaScript调用的Native函数:
```
RCT_REMAP_METHOD(alertUserPromise, resolver:(RCTPromiseResolveBlock)resolver rejecter:(RCTPromiseRejectBlock)reject){
  _resolveBlock=resolver;
  _rejectBlock=reject;
  UIAlertView * alertView=[[UIAlertView alloc] initWithTitle:@"react-native" message:@"使用Promise？" delegate:self cancelButtonTitle:@"关闭" otherButtonTitles:@"继续", nil];
  [alertView show];
}
```
这里使用`RCT_REMAP_METHOD`宏定义Native，他的第一个参数是方法名，后面的参数是方法的实现，在JavaScript调用Promise时并不需要在方法名中体现。
处理结果并使用回调:
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
JavaScript中调用该方法：
```
 _alertUsePromise() {
        RNIOSAlert.alertUserPromise().then((datas)=> {
            console.warn('data', datas);
        }).catch((err)=> {
            console.warn('err', err);
        });
    }
```
这里的then处理的是`Resovle`状态的结果，而catch处理的是`Reject`状态的结果。
也可以使用async/await实现
```
 async  _alertPromise() {
        try {
            var datas = await RNIOSAlert.alertUserPromise();
            console.warn('data', datas);
        } catch (e) {
            console.warn('err', e);
        }
    }
```
`async/await`是两个关键词，用来把`Promises`的思想融入到语言本身。使用他们就不再需要写catch这样的伪同步的代码，直接使用try/catch/return这样的关键词就可以了.
Promises对于回调的使用很便利，尽量避免了JavaScript的回调地狱。

代码地址：https://github.com/jjz/react-native/tree/master/RNIos

