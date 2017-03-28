#解决Android中方法数超过限定的问题
在Android项目中引入过多的jar会出现错误：
>android.dex.DexIndexOverflowException: Cannot merge new index xxxx into a non-jumbo instruction!

这个错误出现的原因是 Android设定的方法数是`65536`个*（DEX 64K problem）*，超过这个方法数，导致dex无法生成，就无法生成APK.
>限制原因:早期的Dalvik VM内部使用short类型变量来标识方法的id，就有了	最大方法数的限制65536。	
解决方法：
## 删除不用的方法，删除不使用的jar
项目维护时间长了，里面会出现不再使用的类和不再使用的方法，建议集中清理下，把不再使用的方法，不再使用的类都清除，这样的好处是代码也干净了，如果方法数超出的不是太多的话通过清理就可以让方法数减少到65536以下，一般来说jar里面的方法数最好，清除一两个无用的jar包就能大大的减少方法数。
## 分包
通过在defaultConfig中设置multiDexEnabled可以开启分包模式，分包之后的Dex就低于了限制数，保证了正常的打包。

```
    defaultConfig {
        multiDexEnabled=true
    }

```
## 忽略方法数限制的检查
```
android.dexOptions {
    jumboMode = true

}
```
设置dexOptions的，不做方法数限制的检查，这样做的缺点是apk无法再低版本的设备上面安装，会出现错误：
>INSTALL_FAILED_DEXOPT

因为我们项目的 minSdkVersion为14,所以我采用了第三种的解决方案，不支持低版本的Android设备。
`以上的解决方法只是针对gradle的设置`





