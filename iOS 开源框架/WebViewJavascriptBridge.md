* GitHub: [WebViewJavascriptBridge](https://github.com/marcuswestin/WebViewJavascriptBridge)
* Star: 12.8k

一个用于在 `WKWebViews`，`UIWebViews`（**Deprecated**） 和 `WebViews` 中的 Obj-C 和 JavaScript 之间发送消息的 iOS/OSX 桥梁。
该框架实现了 Objective-C 和 JavaScript 方法的相互调用，还可以传参和响应回调。（iOS 中，WKWebView 的原生方法不支持回调！）

# 安装

通过 CocoaPods 安装，将以下代码添加到 podfile 文件中并运行 `pod install` 来安装。
```
pod 'WebViewJavascriptBridge', '~> 6.0'
```

# 用法

1. 导入头文件并声明一个实例变量：

```objc
#import <WKWebViewJavascriptBridge.h>
```
...
```objc
@property (nonatomic, strong) WKWebViewJavascriptBridge *bridge;
```

2. 使用 `WKWebView`，`UIWebView`（iOS）或 `WebView`（OSX）实例化 `WKWebViewJavascriptBridge` 对象。

```objc
self.bridge = [WKWebViewJavascriptBridge bridgeForWebView:self.webView];
```

3. 注册一个 Objc 处理程序和一个要调用的 JS 处理程序。

```objc
// JS 调用 OC
// 注册一个让 JS 可以调用 OC 的方法
[self.bridge registerHandler:@"ObjC Echo" handler:^(id data, WVJBResponseCallback responseCallback) {
	NSLog(@"ObjC Echo called with: %@", data);
	responseCallback(data);
}];
// OC 调用 JS
[self.bridge callHandler:@"JS Echo" data:nil responseCallback:^(id responseData) {
	NSLog(@"ObjC received response: %@", responseData);
}];
```

4. 复制和粘贴 `setupWebViewJavascriptBridge` 函数到你的 JS 代码中：

```JavaScript
function setupWebViewJavascriptBridge(callback) {
	if (window.WebViewJavascriptBridge) { return callback(WebViewJavascriptBridge); }
	if (window.WVJBCallbacks) { return window.WVJBCallbacks.push(callback); }
	window.WVJBCallbacks = [callback];
	var WVJBIframe = document.createElement('iframe');
	WVJBIframe.style.display = 'none';
	WVJBIframe.src = 'https://__bridge_loaded__';
	document.documentElement.appendChild(WVJBIframe);
	setTimeout(function() { document.documentElement.removeChild(WVJBIframe) }, 0)
}
```

5. 最后，调用 `setupWebViewJavascriptBridge` 方法，并使用 `bridge` 来注册 JS 处理程序和要调用的 Objc 处理程序：

```JavaScript
setupWebViewJavascriptBridge(function(bridge) {
	
	/* Initialize your app here */
        // OC 调用 JS
        // 在 JS 中可以注册让 OC 调用的方法
	bridge.registerHandler('JS Echo', function(data, responseCallback) {
		console.log("JS Echo called with:", data)
		responseCallback(data)
	})
        // JS 调用 OC 的方法，方法名就是 OC 中提前注册的方法
	bridge.callHandler('ObjC Echo', {'key':'value'}, function responseCallback(responseData) {
		console.log("JS received response:", responseData)
	})
})
```

# API 文档

## Objc API

```objc
[WKWebViewJavascriptBridge bridgeForWebView:(WKWebView*)webView];
```
该方法传递一个给定的 `WKWebView` 参数，让 Objective-C 和 JavaScript 之间建立桥梁。

---

```objc
[bridge registerHandler:(NSString*)handlerName handler:(WVJBHandler)handler];
```
该方法会在 Objc 中注册一个名为 `handlerName` 的处理程序。JavaScript 就可以通过 `WebViewJavascriptBridge.callHandler("handlerName")` 来调用这个处理程序。

示例：

```objc
[self.bridge registerHandler:@"getScreenHeight" handler:^(id data, WVJBResponseCallback responseCallback) {
	responseCallback([NSNumber numberWithInt:[UIScreen mainScreen].bounds.size.height]);
}];
[self.bridge registerHandler:@"log" handler:^(id data, WVJBResponseCallback responseCallback) {
	NSLog(@"Log: %@", data);
}];
```

---

```objc
- (void)callHandler:(NSString*)handlerName;
- (void)callHandler:(NSString*)handlerName data:(id)data;
- (void)callHandler:(NSString*)handlerName data:(id)data responseCallback:(WVJBResponseCallback)responseCallback;
```

以上方法调用名为 `handlerName` 的 JavaScript 方法。如果指定了 `responseCallback` block 块，那么 JavaScript 还可以接收回调。

示例：

```objc
[self.bridge callHandler:@"showAlert" data:@"Hi from ObjC to JS!"];
[self.bridge callHandler:@"getCurrentPageUrl" data:nil responseCallback:^(id responseData) {
	NSLog(@"Current UIWebView page URL is: %@", responseData);
}];
```

---

```objc
[bridge setWebViewDelegate:(id)webViewDelegate];
```

可选，如果你需要响应 web view's lifecycle events，你也可以设置 `WKNavigationDelegate/UIWebViewDelegate` 代理。

---

```objc
[bridge disableJavscriptAlertBoxSafetyTimeout];
```

不安全。通过禁用 `setTimeout` 安全检查来加速桥接消息的传递。仅当您不调用任何 javascript 弹出框功能（警告，确认和提示）时，才能禁用此安全检查。如果您从桥接的 javascript 代码中调用这些功能中的任何一个，则该应用将挂起。

## JavaScript API

```javascript
bridge.registerHandler("handlerName", function(responseData) { ... })
```

在 JavaScript 上注册一个名为 `handlerName` 的处理程序，Objc 就可以通过：
```objc
- (void)callHandler:(NSString*)handlerName;
- (void)callHandler:(NSString*)handlerName data:(id)data;
- (void)callHandler:(NSString*)handlerName data:(id)data responseCallback:(WVJBResponseCallback)responseCallback;
```

这三个实例方法来调用 JS 函数。

示例：

```objc
bridge.registerHandler("showAlert", function(data) { alert(data) })
bridge.registerHandler("getCurrentPageUrl", function(data, responseCallback) {
	responseCallback(document.location.toString())
})
```

---


```javascript
bridge.callHandler("handlerName", data)
bridge.callHandler("handlerName", data, function responseCallback(responseData) { ... })
```
这两个是 JavaScript 调用 Objc 方法的方法，如果 Objc 中注册时设置了 `responseCallback`，那么 JavaScript 就可以接受回调（即调用 Objc 方法时，Objc 会有返回值返回给 JavaScript）。

示例：

```javascript
bridge.callHandler("Log", "Foo")
bridge.callHandler("getScreenHeight", null, function(response) {
	alert('Screen height:' + response)
})
```

---

```javascript
bridge.disableJavscriptAlertBoxSafetyTimeout()
```

在 JavaScript 中调用 `bridge.disableJavscriptAlertBoxSafetyTimeout()` 方法和在 Objc 中调用 `[bridge disableJavscriptAlertBoxSafetyTimeout];` 是同样的效果。


