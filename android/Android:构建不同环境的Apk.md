# Android:构建不同环境的Apk

在Android的开发过程中，常常需要在不同的环境上构建不同的APK，比如我们需要有**开发环境**,**测试环境**和**线上环境**，三种环境会对应不同的服务器地址比如:

```
   public static final String DEV_URL = "http://dev.domain.com/";
   public static final String TEST_URL = "http://test.domain.com/";
   public static final String RELEASE_URL = "http://www.domain.com/";
```

可以使用一个变量配置使用环境：

```
    //0  dev
    //1  test
    //2  release
    public static final int ENV_SETTING = 0;
```

定义一个获取不同环境url的方法，默认为dev环境:

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

这样可以通过设置**ENV_SETTING**构建不同的APK,开发的时候使用默认dev环境，测试的时候给test环境，发布的时候使用release环境。 但是这样做却有一些问题，就是每次必须修改**ENV_SETTING**的值，每次更改都会导致版本库变动，产生新的提交，如果提交之后的话，其他的开发者不一定会知道版本库的变动，导致环境不通产生的错误，还有可能没有环境不对，生产出了错误环境的APK. 有没有办法解决这些问题呢？ 1. 构建不同环境的APK 2. 不需要修改代码 3. 可以选择不同的签名

通过`gradle`可以解决上面的三个问题。

## buildConfigField`buildConfigField`是gradle的一个方法，他的作用是可以给BuildConfig添加变量，赋值等。他的使用方式: >buildConfigField("int", "ENV_SETTING", "0")

这样就给BuildConfig中新增加了一个属性`EN_SETTING`:

```
// Fields from build type: debug
  public static final int ENV_SETTING = 0;
```

在代码中可以直接使用:

```
	//0  dev
    //1  test
    //2  release
    public static final int ENV_SETTING = BuildConfig.ENV_SETTING;
```

`buildConfigField`通过`BuildConfig`类可以做到,把`gradle`里的配置传递给Java代码。

## buildTypes 虽然通过`gradle`中可以配置`ENV_SETTING`变量，通过`buildConfigField`可以让java代码获取到设置的值。但是在buildTypes中我们只能设置两个变量:

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

也就是buildTypes中只有两种配置，在build的时候只能有两个APK:*debug*和*release*，这就需要我们重新定义一种新的buildTypes，用来构建测试环境的APK. 我们新增加一种buildTypes：

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

新增加`buildTypes`叫做`releaseTest`(注意不能使用test开头)。 它继承了`buildTypes.release`的配置，可以通过`buildConfigField`给**releaseTest**里变量重新赋值，它会覆盖`buildTypes.release`的配置。 这样就有了三种不通的build配置。 ## assmble\* gradle通过assmble*命令可以打包不同`buildTypes`的APK，在右侧的gradle工具栏里面的build菜单栏里面可以看到三种不同的assemble:

```
assembleDebug
assembleRelease
assembleTeleaseTest
```

这样我们就可以根据需要构建不同的APK,不需要改代码就可以构建不同环境的APK,而且生成的APK的名字也能区分出来不同的环境：

```
app-debug.apk
app-release.apk
app-releaseTest.apk
```

也可以根据需要设置不同的APK签名。
