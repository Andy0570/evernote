* GitHub: [WebViewJavascriptBridge](https://github.com/marcuswestin/WebViewJavascriptBridge)
* Star: 11000+

> 说明：双方交互之前还有一些初始化代码，此处省略。。。

## 1. JavaScript 调用 Objective-C 方法

> 💡
> Objective-C 注册一个方法，JavaScript 调用该方法。
> JS 调用该方法时可以传参数给OC，OC执行完之后也可以返回参数给JS。



Objective-C 端通过如下方法注册：

##### `[bridge registerHandler:(NSString*)handlerName handler:(WVJBHandler)handler]`



示例：

```objective-c
// 1. JS 调用 OC
// 在 ObjC 中注册一个名为 testObjcCallback 的处理程序
// JavaScript 端就可以通过 WebViewJavascriptBridge.callHandler("testObjcCallback") 方法来调用该处理程序了。
// JS 可以给 OC 传参数，放在 data 中。
// OC 也可以返回参数给 JS，放在 responseCallback 中。
[_bridge registerHandler:@"testObjcCallback" handler:^(id data, WVJBResponseCallback responseCallback) {
    // data 是 JS 传过来的参数内容
    NSLog(@"testObjcCallback called: %@", data);

    if (responseCallback) {
        // OC 返回给 JS 的内容
        responseCallback(@"Response from testObjcCallback");
    }
}];
```



JavaScript 端通过如下方法调用：

##### `bridge.callHandler("handlerName", data)`

##### `bridge.callHandler("handlerName", data, function responseCallback(responseData) { ... })`



示例：

```javascript
<!-- 1. JS 调用 OC -->
bridge.callHandler('testObjcCallback', {'foo': 'bar'}, function(response) {
    log('JS got response', response)
})
```

JS 调用名为 testObjcCallback 的 Objective-C 方法，同时给 Objective-C 传递了参数`'foo': 'bar'`，JS 还接受并打印了Objective-C 返回的参数内容 response。



## 2. Objective-C 调用 JavaScript 方法

> 💡
> JavaScript 注册一个方法，Objective-C 调用该方法。
> OC 调用 JS 方法时，也可以传参数给 JS。
> JS 执行完该方法之后，也可以返回参数给 OC。



JavaScript 端通过如下方法注册：

##### `bridge.registerHandler("handlerName", function(responseData) { ... })`

示例：

```javascript
<!-- 2. OC 调用 JS 的方法 -->
bridge.registerHandler('testJavascriptHandler', function(data, responseCallback) {
    log('ObjC called testJavascriptHandler with', data)
    var responseData = { 'Javascript Says':'Right back atcha!' }
    log('JS responding with', responseData)
    responseCallback(responseData)
})
```





Objective-C 端通过如下方法调用：

##### `[bridge callHandler:(NSString*)handlerName`

##### `[bridge callHandler:(NSString*)handlerName data:(id)data]`

##### `[bridge callHandler:(NSString*)handlerName data:(id)data responseCallback:(WVJBResponseCallback)callback]`

```objective-c
// 2. OC 调用 JS, 同时传参数给 JS。
// 调用名为 handlerName 的 javascript 处理程序。
// 如果给出了 responseCallback 块，则 javascript 处理程序可以响应。
[_bridge callHandler:@"testJavascriptHandler" data:@{ @"foo":@"before ready" }];
```



### 参考

* [WebViewJavascriptBridge 使用 「JS 交互」](https://www.jianshu.com/p/9b101ca587a8)
* [iOS WebViewJavascriptBridge 的使用](https://blog.csdn.net/gezhenrong/article/details/60134343)

