Javascript的异步编程:Promise
Promise对象是CommonJS工作组为异步编程提供的统一接口，是ECMAScript6中提供了对Promise的原生支持，Promise就是在未来发生的事情，使用Promise可以避免回调函数的层层嵌套，还提供了规范更加容易的对异步操作进行控制。提供了`reject`,`resolve`,`then`和`catch`等方法。
## 使用Promise
Promise是ES6之后原生的对象，我们只需要实例化Promise对象就可以直接使用。
实例化Promise:
````
var promise = new Promise(function (resolve, reject) {
    console.log('begin do something');
    if (Math.random() * 10.0 > 5) {
        console.log(" run success");
        resolve();
    } else {
        console.log(" run failed");
        reject();

    }
});
````
这里定义了一个回调方法*function(resolve,reject)*,如果成功了就调用*resolve*,失败了就会调用reject。
`Promise.prototype.then`是Promise执行完之后的回调，可以用then方法分别指定resolve和reject的回调。
```
promise.then(function () {
    console.log(' resolve from promise');
}, function () {
    console.log(' reject from promise');
});
```
执行结果一:
```
begin do something
 run success
 resolve from promise
```
执行结果二:
```
begin do something
 run failed
 reject from promise
```
## 使用Promise进行网络请求
```
getRequest = function (url) {
    var promise = new Promise(function (resolve, reject) {
        var request = require('request');
        request(url, function (error, respones, body) {
            if (error) {
                reject(error);
                return;
            }
            if (respones.statusCode == 200) {
                resolve(body)

            } else {
                reject(respones.status);

            }
        });
    });
    return promise;

};

getRequest("https://github.com/").then(function (result) {
    console.log(result);
}, function (error) {
    console.error('error', error);
});

```
使用Promise进行网络请求，也可以使用Promise在浏览上实现Ajax请求。


代码地址：https://github.com/jjz/node

