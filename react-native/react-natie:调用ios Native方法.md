#react-natie调用ios native
React Native在手机之初，就考虑到了React Native提供的API,不能够完全的覆盖平台的所有API.因此在使用React Native的时候就需要调用Native的方法，Android上面对应Java方法，IOS上对应Object-C方法。
以调用系统的`Alert`为例，看下怎么使用JavaScript代码调用Object-C的Native方法的。

##RCTBridgeModule
`RCT`是ReaCT的缩写，React Native中Object-C相关的命名均以RCT开头。
`RCTBridgeModule`是定义好的`protocol`，实现改协议的类，会注册到Object-C对应的Bridge中，Object-C Bridge上层负责和Object-C通信，下层负责和JavaScript Bridge通信，而JavaScript Bridge负责和JavaSxript通信.
通过Object-C Bridge和JavaScript Bridge就可以实现JavaScript和Object-C的相互调用。
定义`RNIOSAlert`类用来现实`RCTBridgeModule`协议。
```
#import <Foundation/Foundation.h>
#import <UIKit/UIKit.h>
#import "RCTBridgeModule.h"

@interface RNIOSAlert : NSObject<RCTBridgeModule>

@end

```
##RCT_EXPORT_MODULE
所有实现`RCTBridgeModule`的类都必须显示的`include`宏命令`RCT_EXPORT_MODULE()`。
`RCT_EXPORT_MODULE`的作用是，自动注册一个Module当briage加载的时候。这个Module可以在JavaScript中调用。
`RCT_EXPORT_MODULE`宏命令的定义:
```
#define RCT_EXPORT_MODULE(js_name) \
RCT_EXTERN void RCTRegisterModule(Class); \
+ (NSString *)moduleName { return @#js_name; } \
+ (void)load { RCTRegisterModule(self); }

```
可以到`RCT_EXPORT_MODULE`接受字符串作为其module的名称，如果不设置参数的话默认就是类名作为名称。
引入`RCT_EXPORT_MODULE`:
```


#import "RNIOSAlert.h"

@implementation RNIOSAlert

RCT_EXPORT_MODULE();

@end

```
##RCT_EXPORT_METHOD
`RCT_EXPORT_METHOD`是用来定义JavaScript调用方法的宏。`RCT_EXTERN_METHOD`调用了宏`RCT_EXTERN_REMAP_METHOD`,最终调用宏`RCT_EXTERN_REMAP_METHOD`:
```
#define RCT_EXTERN_REMAP_METHOD(js_name, method) \
  + (NSArray<NSString *> *)RCT_CONCAT(__rct_export__, \
    RCT_CONCAT(js_name, RCT_CONCAT(__LINE__, __COUNTER__))) { \
    return @[@#js_name, @#method]; \
  }
```
它的作用是在`RCT_EXPORT_MODULE`定义的Module下面，定义可以调用的JavaScript方法。
`RCT_EXPORT_MODULE`的定义，需要写入方法名,参数，已经完整的实现，例如:
```
#import "RNIOSAlert.h"

@implementation RNIOSAlert

RCT_EXPORT_MODULE();

RCT_EXPORT_METHOD(show:(NSString *)msg){

  UIAlertView * alertView=[[UIAlertView alloc] initWithTitle:@"react-native" message:msg delegate:nil cancelButtonTitle:@"关闭" otherButtonTitles:nil, nil];
    [alertView show];

}
@end

```
##JavaScript调用
在JavaScript中调用Object-C定义的方法，需要先导入`NativeModules`,再使用`RNIOSAlert`.
完整代码如下:
```
import {   
    StyleSheet,
    Text,
    View,
    NativeModules,
    TouchableOpacity
} from "react-native";

var RNIOSAlert = NativeModules.RNIOSAlert;


class RNIos extends Component {
    render() {

        return (
            <View style={styles.container}>
                <TouchableOpacity onPress={()=>RNIOSAlert.show('from react native ')}>
                    <Text>Alert</Text>
                </TouchableOpacity>
            </View>
        );
    }
}
```
成功调用`Alert`.

##参数
`RCT_EXPORT_METHOD`支持需要JSON支持的数据类型，JavaScript中的参数和Object-C中的参数是一一对应的关系。

* string -> NSString
* number -> (NSInteger,float,double,CGFloat,NSNumber)
* boolean -> (BOOL,NSNumber)
* array -> NSArray
* object -> NSDictionary
* function -> RCTResponseSenderBlock

另外React Navite还提供了`RCTConvert`,详情的代码可以参照[https://github.com/facebook/react-native/blob/master/React/Base/RCTConvert.h](https://github.com/facebook/react-native/blob/master/React/Base/RCTConvert.h),他的作用可以把传入的参数转换为需要的数据类型。
我们在Object-C中定义一个方法，该方法接收`NSDictionary`参数：
```

RCT_EXPORT_METHOD(showTime:(NSDictionary*)dict){
  NSDate * date =[RCTConvert NSDate:dict[@"time"]];
  UIAlertView * alertView=[[UIAlertView alloc] initWithTitle:@"react-native" message:[date description] delegate:nil cancelButtonTitle:@"关闭" otherButtonTitles:nil, nil];
  [alertView show];  
}

```

在JavaScript中的调用:
```
......
<TouchableOpacity onPress={()=> {
                    var date = new Date();
                    RNIOSAlert.showTime(
                        {
                            time: date.getTime()
                        }
                    )
                }}>
                    <Text>Time</Text>
                </TouchableOpacity>
......
```
这个在Object-C中可以直接使用`RCTConvert`转换JavaScript传入的**Date**参数。

