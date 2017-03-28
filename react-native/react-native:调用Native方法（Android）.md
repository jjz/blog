react-native:调用Native方法（Android）
======================================

有的时候我们使用React Native无法满足一些使用特定场景，这个时候就需要使用原生的Android方法，比如一些耗时的写操作，操作数据库或者多线程操作等。 React Native可以直接调用系统的API（java方法）,实现JavaScript与java语言的通讯，如果React Native中没有满足我们需求的Api,可以封装原生的方法提供JavaScript调用。 JavaScript和java通信是通过`bridge`实现的，在java层和JavaScript层的bridge分别存有相同的一份模块配置表。Java与JavaScript相互通信时，通过bridge里的配置表将所调用模块方式转为`{moduleID,methodID,args}`的形式传递给处理层，处理层通过bridge里的配置表找到对应的方法执行，如果有callback，则回传给调用层,如果没有执行就结束。

我们通过JavaScript调用Toast的例子来看下，JavaScript如何调用Java代码的。

新建一个项目: >react-native init RNAndroid

在android的项目目录下面新建一个类*RNToastModule*,此类需要继承`ReactContextBaseJavaModule`。

## ReactContextBaseJavaModule`ReactContextBaseJavaModule`是一个抽象类，是用来被JavaScript调用对象的父类，我们需要Override一些`ReactContextBaseJavaModule`的方法。

首先要Override `getName()`方法：

```
   @Override
   public String getName() {
       return "RNToastAndroid";
   }
```

这个方法的返回值就是JavaScript中调用的名称，比如我们命名为*RNToastAndroid*,在JavaScript中可以这样调用：

```
var {NativeModules}=require('react-native');
var rnToastAndroid = NativeModules.RNToastAndroid;
```

然后我们可以选择性的覆盖`getConstants()`方法： 这个方法的用在JavaScript和Java直接定义公用常量的，它使用key-value的方式保存。 在Java中定义两个变量：

```
   private static final String DURAION_SHORT_KEY = "SHORT";
   private static final String DURAION_LONG_KEY = "LONG";
```

在`getConstants()`中给两个字符串赋值:

```
    @Override
    public Map<String, Object> getConstants() {
        final Map<String, Object> constants = new HashMap<>();
        constants.put(DURAION_SHORT_KEY, Toast.LENGTH_SHORT);
        constants.put(DURAION_LONG_KEY, Toast.LENGTH_LONG);
        return constants;
    }

```

我们把Toast的两个常量放在了 `constants`中。 在JavaScript可以这样调用:

```
rnToastAndroid.show('Hello Toast of native', rnToastAndroid.SHORT);
```

最后我们定义一个React调用的方法：

```
    @ReactMethod
    public void show(String message, int duration) {
        Toast.makeText(getReactApplicationContext(), message, duration).show();

    }
```

这个使用了`annotation`定义的方式必须加上`@ReactMethod`。 这里的参数只能React Navive定义的参数。

## ReactMethod的对应参数`@ReactMethod`中传的参数必须是JavaScript和Java对应的。

```
Boolean -> Bool
Integer -> Number
Double -> Number
Float -> Number
String -> String
Callback -> function
ReadableMap -> Object
ReadableArray -> Array
```

## 注册ReactPackage 新建一个*RNJavaReactPackage*类，继承ReactPackage。

```
    @Override
    public List<NativeModule> createNativeModules(ReactApplicationContext reactContext) {
        List<NativeModule> modules = new ArrayList<>();
        modules.add(new RNToastModule(reactContext));
        return modules;
    }

    @Override
    public List<Class<? extends JavaScriptModule>> createJSModules() {
        return Collections.emptyList();
    }

    @Override
    public List<ViewManager> createViewManagers(ReactApplicationContext reactContext) {
        return new ArrayList<>();
    }
```

*RNJavaReactPackage*创建了一个NativeModule的List。把*RNToastModule*的实例都添加进去提供给JavaScript层调用。

## 添加ReactPackage~~在`android/app/src/main/java/com/your-app-name/`中有个*MainActivity.java*其中的`getPackages()`方法用来返回用来的ReactPackage包,添加定义好的*RNJavaReactPackage*的实例~~ 以上内容已经在`0.31.0`中更新。 新版本使用`ReactNativeHost`替换`ReactInstanceManager`,ReactNativeHost是设置Android相关配置的类。 需要在`Application`中实例化。 首先实现新建一个`ReactNativeHost`的实例并添加*RNJavaReactPackage*的实例:

```
private final ReactNativeHost mReactNativeHost = new ReactNativeHost(this) {
   @Override   
   protected boolean getUseDeveloperSupport() {   
       return BuildConfig.DEBUG;   
   }
   @Override   
   protected List<ReactPackage> getPackages() {
         return Arrays.<ReactPackage>asList(
                new MainReactPackage(),         
               new RNJavaReactPackage()       
         );
     }
};
```

实现`ReactApplication`:

```
public class MainApplication extends Application implements ReactApplication {    
    @Override    
    public ReactNativeHost getReactNativeHost() {      
        return mReactNativeHost;   
   }
}
```

## JavaScript中调用 在JavaScript显示Toast:

```
'use strict';

var {NativeModules}=require('react-native');
var rnToastAndroid = NativeModules.RNToastAndroid;

rnToastAndroid.show('Hello Toast of native', rnToastAndroid.SHORT);
```

这样就完成了从JavaScript中直接调用了Java中定义的方法。

代码地址：https://github.com/jjz/react-native/tree/master/RNAndroid
