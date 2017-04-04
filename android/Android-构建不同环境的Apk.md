# Android-构建不同环境的Apk

在开发`Android`过程中，常常需要构建不同环境的Apk，比如我们有可能会需要有三个环境

* 开发环境
* 测试环境
* 线上环境

三种环境对应不同的服务器地址:
```
   public static final String DEV_URL = "http://dev.domain.com/";
   public static final String TEST_URL = "http://test.domain.com/";
   public static final String RELEASE_URL = "http://www.domain.com/";
```
可以使用一个变量配置使用不同的环境：
```
    //0  dev
    //1  test
    //2  release
    public static final int ENV_SETTING = 0;
```
定义一个获取不同环境url的方法，默认为`dev`环境:
```
    public static String getBaseUrl() {
        switch (ENV_SETTING) {
            case 1:
                return TEST_URL;
            case 2:
                return RELEASE_URL;
            default:
                return DEV_URL;
        }
    }
```

这样可以通过设置**ENV_SETTING**构建不同的Apk:
* 开发的时候使用默认dev环境
* 测试的时候给test环境
* 发布的时候使用release环境

但是这样做却有一些问题，就是每次必须修改**ENV_SETTING**的值，每次更改都会导致版本库变动，产生新的提交，如果提交之后的话，其他的开发者不一定会知道版本库的变动，导致环境不同产生错误，还有可能环境不对，生产出了错误环境的Apk. 有没有办法解决以下问题呢？

1. 可以构建不同环境的Apk 
2. 不需要修改代码 
3. 可以使用不同的签名
利用`gradle`可以解决上面的三个问题，下面就看下如何使用`gradle`解决上面的问题吧！

## buildConfigField
`buildConfigField`是gradle的一个方法，他的作用是可以给BuildConfig添加变量，赋值等。它的使用: 
>buildConfigField("int", "ENV_SETTING", "0")

这样就给`BuildConfig`中新增加了一个变量`EN_SETTING`，新增的代码如下:
```
// Fields from build type: debug
  public static final int ENV_SETTING = 0;
```
在代码中使用`BuildConfig`新增的变量`EN_SETTING`:
```
	//0  dev
    //1  test
    //2  release
    public static final int ENV_SETTING = BuildConfig.ENV_SETTING;
```

`buildConfigField`通过`BuildConfig`类可以把`gradle`里的配置传递给Java代码。只是传递给了Java值还不能解开始说的三个问题。下面再看下如何在`gradle`中配置不同的环境。

## buildTypes 
虽然通过`gradle`中可以配置`ENV_SETTING`变量，通过`buildConfigField`可以让java代码获取到设置的值。但是在buildTypes中只能设置两个变量`release`和`debug`:
```
buildTypes {
        release {
            signingConfig signingConfigs.release
            buildConfigField("int", "ENV_SETTING", "2")
        }
        debug {
            buildConfigField("int", "ENV_SETTING", "0")
        }


    }
```
`buildTypes`中只有两种配置，在build的时候只能生产出两个Apk:*debug*和*release*，需要第三种环境的Apk,就需要定义一种新的`buildTypes`，用来构建测试环境的Apk. 新增加一种`buildTypes`：
```
 buildTypes {
        release {
            signingConfig signingConfigs.release
            buildConfigField("int", "ENV_SETTING", "2")
        }
        debug {
            buildConfigField("int", "ENV_SETTING", "0")
        }
        releaseTest.initWith(buildTypes.release)
        releaseTest {
            buildConfigField("int", "ENV_SETTING", "1")
        }

    }
```
新增加`buildTypes`叫做`releaseTest`(注意不能使用test开头)。 它继承了`buildTypes.release`的配置，可以通过`buildConfigField`给`ENV_SETTING`重新赋值，它会覆盖`buildTypes.release`的配置。 这样就有了三种不同的build配置。 
## assmble* 
`gradle`通过`assmble*`命令可以打包不同`buildTypes`的Apk，在右侧的gradle工具栏里面的build菜单栏里面可以看到三种不同的`assemble`:

* assembleDebug
* assembleRelease
* assembleTeleaseTest

这样就可以根据需要构建不同的Apk,不需要更改代码就可以构建不同环境的Apk,而且生成的Apk的名字也能区分出不同的环境，通过上面的配置可以构建出三种Apk：

* app-debug.apk
* app-release.apk
* app-releaseTest.apk

还可以根据上面的配置需要设置不同的Apk签名，通过`signingConfig`进行设置。


