#  Apache Http Client 在Android SDK 23中被移除
把complieSdkVersion 升级为23的时候遇到了一个问题：

>import org.apache.http.Header

报错，无法找到org.apache.http包。
原来是`Android 6.0`的sdk去掉了对于Apache Http Client的支持。
Android 6.0去掉了对Apache Http Client的支持，Google的建议是用*HttpURLConnection*（API level 9以上），Google解释说这个API的效率更高，它可以通过压缩和响应缓存减少网络的使用，可以最大限度的降低耗电量。
如果需要继续使用Apache HTTP API,你必须先在gradle文件中声明依赖项：
```
android {
    useLibrary 'org.apache.http.legacy'
}
```
参考文档：https://developer.android.com/intl/zh-cn/about/versions/marshmallow/android-6.0-changes.html# behavior-apache-http-client

