#Android:java和javascript相互调用
在当前的Android开发中，会使用大量的H5(html5+css3+js),和Native一起的混合开发模式（Hybrid），在Hybrid开发的过程中，H5部分开发效率高和移植便利性为主，但是在一些地方使用h5的确不容易实现，这个时候就需要调用Java方法来完成某些功能，就会遇到Javascript和Java相互调用，用Java的方法实现那些Javascript代码不容易，不好完成的任务，比如，异步线程，调用数据库等.....

在Android 4.2之前可以使用`addjavascriptinterface`的方式注入原生的Java中,给JavaScript调用,但是这种方案却会有一定的安全风险，在页面中执行一些不可信的Javascript代码即可能控制用户的手机,详情见:[WebView中接口隐患与手机挂马利用](http://drops.wooyun.org/papers/548)
在4.2之后Android提供了`@JavascriptInterface`对象注解的方式建立Javascript对象和android原生对象的绑定,提供给javascript调用的函数必须带有`@JavascriptInterface`。

因为目前4.0系统的手机市场占有量已经很低了，因此我们使用`minSdkVersion`为17，只需要支持4.2版本以上的手机，看下Java和Javascript怎么通信的。

##加载本地html文件
有的时候我们在使用webview开发的时候会使用本地的html文件，在这里为了方便我们把html文件都放在`assets`文件夹中，使用本地加载的方式，不需要server支持。
先定义一个html文件:
```
<!DOCTYPE html>
<html>
	<body>
		<h1>this is html</h1>
	</body>
</html>
```
使用`file:///android_asset/index.html`加载到webview中:
```
    private void initView() {
        webView = (WebView) findViewById(R.id.webView);
        webView.loadUrl("file:///android_asset/index.html");
    }
```

##Javascript调用Java方法
以Android的Toast的为例，从Javascript代码中调用系统的Toast。
我们定义一个AndroidToast的Java类,它有一个show的方法用来显示Toast:
```
public class AndroidToast {
        @JavascriptInterface
        public void show(String str) {
            Toast.makeText(MainActivity.this, str, Toast.LENGTH_SHORT).show();
        }
    }
```
需要对WebView设置一些参数，开启JavaScipt，注册JavascriptInterface：
```
private void initView() {
        webView = (WebView) findViewById(R.id.webView);

        WebSettings webSettings = webView.getSettings();
        webSettings.setJavaScriptEnabled(true);
        webSettings.setDefaultTextEncodingName("UTF-8");
        webView.addJavascriptInterface(new AndroidToast(), "AndroidToast");
        webView.loadUrl("file:///android_asset/index.html");
 }
```
`addJavascriptInterface`的作用是把AndroidToast类映射为Javascript中的AndroidToast对象。

在Javascript中调用Java代码:
```
function toastClick(){
        window.AndroidToast.show('from js');
}
```
通过window的属性可以找到Java映射的对象AndroidToast,调用它的show方法。
注意这里传输的数据只能是基本数据类型和string,可以传输string意味着我们可以使用`json`传输结构化数据。
##Javascript调用Java有返回值
如果想从Javascript调的方法里面获取到返回值，只需要定义一个带返回值的`@JavascriptInterface`方法:
```

    public class AndroidMessage {
        @JavascriptInterface
        public String getMsg() {
            return "form java";
        }
    }
```
添加Javascript的映射Webview:
>webView.addJavascriptInterface(new AndroidMessage(), "AndroidMessage");

Javascript直接调用Java方法：
```
function showAlert(){
        var str=window.AndroidMessage.getMsg();
        console.log(str);
 }
```

##Java调用Javascript方法
Java在调用js的时候，使用的是`WebView.loadUrl()`方法，可以直接在HTML页面里面执行JavaScript方法，首先定义一个Javascript方法给Java调用：
```
function callFromJava(str){
        console.log(str);
    }
```
Java端调用Javascript方法:
```
public void  javaCallJS(){
        webView.loadUrl("javascript:callFromJava('call from java')");
    }
```
可以在loadUrl中直接给Javascript方法直接传值，如果Javascript方法有返回值，而WebView.loadUrl()是无法获取到返回值的，如果需要Javascript返回值给Java，可以定义一个Java方法给JavaScript调用把返回值传递给Java。
注意`WebView.loadUrl()`必须在`Ui线程`中运行，不然会会报错。

项目地址：https://github.com/jjz/android/tree/master/JSBriage


