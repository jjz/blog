#用gradle自动配置百度地图Key
在开发中需要使用百度地图，百度地图会需要有两个key:*release*和*debug*,需要使用对应的签名证书的`SHA1`在百度地图的开发平台上申请（http://lbsyun.baidu.com/apiconsole/key）。
获取签名证书`SHA1`的方式：
cd到`.android`所在的目录:
>keytool -list -v -keystore debug.keystore

同样的方式可以到`release`签名证书的SHA1。
现在我们就有了两个百度地图的key:`release-key`和`debug-key`
一般情况需要在`AndroidManifest`指定对于的百度地图的key才能在APP中正确的显示百度地图。
```
		<meta-data
            android:name="com.baidu.lbsapi.API_KEY"
            android:value="debug-key" />
```
这里有产生两个用的时候很不方便的问题:
	1.每个开发者都有一个debug的签名证书，每个人都需要申请一个百度地图key，每个人都需要配置。
	2.在编译reasle的时候需要切换把`com.baidu.lbsapi.API_KEY`换成release-key
解决这两个问题可以通过强大的`gradle`，实现无缝的自动的在项目中配置release-key和debug-key。

##使用一个同一个debug-key
把一个已经生成百度地图key的debug签名证书提交到版本库中，指定每个开发者使用这个签名证书debug。
```
android {
    compileSdkVersion 23
    buildToolsVersion '23.0.2'
 	 signingConfigs {
        release {
            storeFile file("${rootDir}/release")
            storePassword ""
            keyAlias ""
            keyPassword ""
        }
        debug {
            storeFile file("${rootDir}/debug.keystore")
        }
    }
    sourceSets {
        main {
            jni.srcDirs = []
        }
    }
    }
```
在`android`的`signingConfigs`中可以配置release的签名证书和debug的签名证书,一般都是指定release的签名证书,这里也指定下debug的签名证书,`${rootDir}`是当前项目的主目录，这样就指定了所有的开发人员都使用一个debug签名证书。
在配置`com.baidu.lbsapi.API_KEY`的时候只需要配置一个debug-key就可以了。

##自动切换reasle-key和debug-key

虽然设置了`com.baidu.lbsapi.API_KEY`为debug-key但是在发布项目的时候还需要重新设置为`reasle-key`，这样的操作不仅麻烦而且还容易出错，经常出现发布之后的版本百度地图无法显示，因为使用了reasle的签名证书，却没有使用reasle-key。
下面使用`gradle`让项目自动的切换reasle-key和debug-key,在发布版本的时候不需要再重新配置。
在`AndroidManifast.xml`中配置：
```
 		<meta-data
            android:name="com.baidu.lbsapi.API_KEY"
            android:value="${BAIDU_KEY_VALUE}" />
```
这里给`com.baidu.lbsapi.API_KEY`的value设置了一个变量*BAIDU_KEY_VALUE*。

在`build.gradle`中给*BAIDU_KEY_VALUE*赋值：
```
android {
    compileSdkVersion 23
    buildToolsVersion '23.0.2'
    
     buildTypes {
        release {     
            manifestPlaceholders = [BAIDU_KEY_VALUE: "release-key"]
        }
        debug {
            manifestPlaceholders = [BAIDU_KEY_VALUE: "debug-key"]
        }
    }
}
```
`manifestPlaceholders`是gradle的一个功能，可以在AndroidManifest中定义一个变量，定义的语法是`${BAIDU_KEY_VALUE}`,在build.gradle中可以给这里变量赋值，这个语法使用方便，语法简单。
这样在运行项目的时候在debug中就会使用百度地图的debug-key，在发布的时候就会使用百度地图的realest-key,不再需要手动切换。

