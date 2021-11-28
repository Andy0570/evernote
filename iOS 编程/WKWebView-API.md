**WKWebView** 对象可以显示交互式Web内容，例如应用内浏览器。你可以使用 **WKWebView** 类将Web内容嵌入到你的应用程序中。 为此，创建一个 **WKWebView** 对象，像设置视图一样设置它，并向其发送加载Web内容的请求。



## 预览

> 重要
>
> 从 iOS 8.0 和 OS X 10.10 开始，在你的APP中使用 **WKWebView** 添加网页内容，不要使用 **UIWebView** 或 **WebView**。



使用 `initWithFrame:configuration:` 方法创建了一个新的 **WKWebView** 对象之后，你需要加载web内容。使用 `loadHTMLString:baseURL:` 方法加载本地 HTML 文件或者使用 `loadRequest:` 方法开始加载 web 内容。使用 `stopLoading` 方法停止加载，并且使用 `loading` 属性查看 web 视图是否正在加载中。为对象设置委托属性以遵守 **WKUIDelegate** 协议，以跟踪Web内容的加载。

要允许用户在 web 历史页面中前进或者后退，为按钮设置使用 `goBack`和`goForward`方法的动作。当用户不能在某个方向上再移动时，使用`canGoBack`和`canGoForward`属性禁用按钮。

默认情况下，web 视图会自动将出现在 web 内容中的电话号码转换为电话链接。当电话链接被点击时，电话应用程序就会启动并拨打该号码。要关闭这个默认的行为，用 `WKDataDetectorTypes`  设置 `dataDetectorTypes` 属性以不包含 `WKDataDetectorTypePhoneNumber` 标志。 

你还可以使用 `setMagnification：centeredAtPoint：` 以编程方式设置Web内容第一次在Web视图中显示的缩放比例。 此后，用户可以使用手势来改变比例。



## Symbols


### 初始化web视图

* `configuration`
  用于初始化Web视图的配置副本。
* `- initWithFrame:configuration:`
  用指定的 frame 和 configuration 初始化web视图 。
* `- initWithCoder:`

---
### 查看web信息

* `scrollView`
  与web视图相关联的滚动视图。
* `title`
  页面标题。
* `URL`
  活动网址。
* `customUserAgent`
  自定义用户代理字符串。
* `serverTrust`
  用于当前待导航的 SecTrustRef 对象。

---

### 设置委托

* `navigationDelegate`
  web视图的导航代理。
* `UIDelegate`
  web视图的用户界面委托。

---

### 加载内容

* `estimatedProgress`
  当前导航的哪几个部分已经加载的估计值（double：0.0~1.0）。
* `hasOnlySecureContent`
  布尔值，指示页面上的所有资源是否通过安全加密的连接加载。
* `- loadHTMLString:baseURL:`
  设置网页内容和 base URL。
* `loading`
  布尔值，显示当前视图是否正在加载。
* `- reload`
  重新加载当前页面
* `- reloadFromOrigin`
  重新加载当前页面，如果可能，使用缓存验证条件执行端到端重新验证。
* `- stopLoading`
  停止加载当前页面所有资源。
* `- loadData:MIMEType:characterEncodingName:baseURL:`
  设置网页内容和 base URL。
* `- loadFileURL:allowingReadAccessToURL:`
  找到文件系统上所请求的文件URL

---

### 缩放内容

* `allowsMagnification`
  布尔值，表示放大手势是否会改变网页视图的放大倍数。
* `magnification`
  页面内容当前缩放因子。
* `- setMagnification:centeredAtPoint:`
  按指定的因子缩放页面内容，并将结果居中在指定的点上。

---

### 导航

* `allowsBackForwardNavigationGestures`
  布尔值，指示水平滑动手势是否会触发后退列表导航。默认为：NO。
* `backForwardList`
  网页视图的后退列表。（之前访问过的 web 页面的列表）
* `canGoBack`
  布尔值，指示后退列表中是否有可被导航到的后退项。
* `canGoForward`
  布尔值，指示后退列表中是否有可导航的前进项。
* `allowsLinkPreview`
  布尔值，用于确定是否按下链接可以显示链接目标的预览。
* `- goBack`
  导航到后退列表中的后退项中。
* `- goForward`
  导航到后退列表中的前进项中。
* `- goToBackForwardListItem:`
  导航到后退列表中的某一个网页项，并将其设置为当前项。
* `- loadRequest:`
  导航到请求的URL地址

---

### 执行 JavaScript

* `- evaluateJavaScript:completionHandler:`
  评估 **JavaScript** 字符串。**Objective-C 调用 JavaScript 方法**。

---

### 实例方法

* `- goBack`
  导航到后退列表中的后退项中。
* `- goForward`
  导航到后退列表中的前进项中。
* `- reload`
  重新加载当前页面
* `- reloadFromOrigin`
  重新加载当前页面，如果可能，使用缓存验证条件执行端到端重新验证。
* `- stopLoading`
  停止加载当前页面所有资源。

---





# WKWebViewConfiguration API 

用于初始化Web视图的属性集合。



## 预览

使用  **WKWebViewConfiguration** 类，你可以确定网页呈现的速度，媒体播放的处理方式，用户可以选择的项目的粒度等等。

**WKWebViewConfiguration** 仅在首次初始化Web视图时使用。 当web视图被创建以后，你就无法再使用此类来更改Web视图的配置信息了。



## Symbols

### 配置新的Web视图的属性

* `applicationNameForUserAgent`
  在用户代理字符串中使用的应用程序的名称。
* `preferences`
  Web视图要使用的首选项对象。
* `processPool`
  从中获取视图的Web内容进程的进程池。
* `userContentController`
  与网页视图关联的用户内容控制器。
* `websiteDataStore`
  由网页视图使用的存储的网站数据。



---

### 确定页面可扩展性

* `ignoresViewportScaleLimits`
  布尔值，用于确定 **WKWebView** 对象是否应始终允许缩放网页。

---

### 设置渲染首选项

* `suppressesIncrementalRendering`
  布尔值，指示网络视图是否在【内容渲染完全加载到内存之前】禁止内容呈现。默认值：NO。

---



### 设置媒体播放首选项

* `allowsInlineMediaPlayback`
  布尔值，指示HTML5视频是否内嵌播放或使用native全屏控制器。
* `allowsAirPlayForMediaPlayback`
  是否允许 **AirPlay**。
* `allowsPictureInPictureMediaPlayback`
  HTML5视频是否可以播放画中画。
* `mediaTypesRequiringUserActionForPlayback`
  确定哪些媒体类型需要用户手势才能开始播放。
* `WKAudiovisualMediaTypes`
  枚举类型：需要用户手势开始播放的媒体类型

  ```objectivec
  typedef enum WKAudiovisualMediaTypes : NSUInteger {
      WKAudiovisualMediaTypeNone = 0,
      WKAudiovisualMediaTypeAudio = 1 << 0,
      WKAudiovisualMediaTypeVideo = 1 << 1,
      WKAudiovisualMediaTypeAll = NSUIntegerMax
  } WKAudiovisualMediaTypes;
  ```

---

### 设置选择粒度

* `selectionGranularity`
  用户可以在网页视图中交互地选择内容的粒度级别。
* `WKSelectionGranularity`
  枚举类型：交互式创建和修改选择的粒度。
  ```objectivec
  typedef enum WKSelectionGranularity : NSInteger {
      WKSelectionGranularityDynamic,	//选择粒度根据选择而自动变化。
      WKSelectionGranularityCharacter //选择端点可以放置在任何字符边界上
  } WKSelectionGranularity;
  ```

---



### 选择用户界面方向

* `userInterfaceDirectionPolicy`
  用户界面元素的方向。
* `WKUserInterfaceDirectionPolicy`
  枚举类型：用于确定Web视图中用户界面元素的方向性策略。
  ```objectivec
  typedef enum WKUserInterfaceDirectionPolicy : NSInteger {
      WKUserInterfaceDirectionPolicyContent,	// 方向遵循CSS / HTML / XHTML规范
      WKUserInterfaceDirectionPolicySystem // 方向遵循视图的userInterfaceLayoutDirection属性
  } WKUserInterfaceDirectionPolicy;
  ```

  

---



### 识别数据类型

* `dataDetectorTypes`
  所需的数据检测类型。
* `WKDataDetectorTypes`
  枚举类型：检测到的数据类型。
  ```objectivec
  typedef enum WKDataDetectorTypes : NSUInteger {
      WKDataDetectorTypeNone = 0,
      WKDataDetectorTypePhoneNumber = 1 << 0,
      WKDataDetectorTypeLink = 1 << 1,
      WKDataDetectorTypeAddress = 1 << 2,
      WKDataDetectorTypeCalendarEvent = 1 << 3,
      WKDataDetectorTypeTrackingNumber = 1 << 4,
      WKDataDetectorTypeFlightNumber = 1 << 5,
      WKDataDetectorTypeLookupSuggestion = 1 << 6,
      WKDataDetectorTypeAll = NSUIntegerMax,
      WKDataDetectorTypeSpotlightSuggestion = WKDataDetectorTypeLookupSuggestion
  } WKDataDetectorTypes;
  ```

  


# WKNavigationDelegate API

**WKNavigationDelegate** 协议方法可以帮助你实现在Web视图接受，加载和完成导航请求的过程中触发的自定义行为。



## 初始化导航和跟踪加载进度

```objectivec
//  页面开始加载 web 内容时调用
- (void)webView:(WKWebView *)webView didStartProvisionalNavigation:(WKNavigation *)navigation;

//  当 web 内容开始返回时调用
- (void)webView:(WKWebView *)webView didCommitNavigation:(WKNavigation *)navigation;

//  页面加载完成之后调用
- (void)webView:(WKWebView *)webView didFinishNavigation:(WKNavigation *)navigation;

//  页面加载失败时调用 ( web 视图加载内容时发生错误)
- (void)webView:(WKWebView *)webView didFailProvisionalNavigation:(WKNavigation *)navigation withError:(NSError *)error;

// web 视图导航过程中发生错误时调用。
- (void)webView:(WKWebView *)webView didFailNavigation:(WKNavigation *)navigation withError:(NSError *)error;

// 当 Web 视图的 Web 内容进程终止时调用。
- (void)webViewWebContentProcessDidTerminate:(WKWebView *)webView;
```

导航错误：[NSURLErrorDomain Code=-999](https://yq.aliyun.com/articles/37066) 
原因：webview 的上一个请求还没有加载完成，下一个请求发起了，此时 webview 会取消掉之前的请求，因此会回调导航失败错误（NSURLErrorCancelled = -999）。


## 响应服务器操作

```objectivec
// 收到服务器重定向之后调用 (接收到服务器跳转请求)
- (void)webView:(WKWebView *)webView didReceiveServerRedirectForProvisionalNavigation:(WKNavigation *)navigation;
```



## 实例方法

* `- webView:didReceiveAuthenticationChallenge:completionHandler:`
  当 Web 视图需要响应认证挑战(authentication challenge)时调用。



当使用 HTTPS 协议加载 web 内容时，使用的证书不合法或者证书过期时需要使用该方法：

```objectivec
- (void)webView:(WKWebView *)webView didReceiveAuthenticationChallenge:(NSURLAuthenticationChallenge *)challenge completionHandler:(void (^)(NSURLSessionAuthChallengeDisposition disposition, NSURLCredential *credential))completionHandler {
    if ([challenge.protectionSpace.authenticationMethod isEqualToString:NSURLAuthenticationMethodServerTrust]) {
        if ([challenge previousFailureCount] == 0) {
            NSURLCredential *credential = [NSURLCredential credentialForTrust:challenge.protectionSpace.serverTrust];
            completionHandler(NSURLSessionAuthChallengeUseCredential, credential);
        } else {
            completionHandler(NSURLSessionAuthChallengeCancelAuthenticationChallenge, nil);
        }
    } else {
        completionHandler(NSURLSessionAuthChallengeCancelAuthenticationChallenge, nil);
    }
}
```



## 允许导航

通常用于处理跨域的链接能否导航
```objectivec
// 在发送请求之前，决定是否允许或取消导航。
- (void)webView:(WKWebView *)webView decidePolicyForNavigationAction:(WKNavigationAction *)navigationAction decisionHandler:(void (^)(WKNavigationActionPolicy))decisionHandler {
    NSLog(@"%s", __FUNCTION__);
    // 如果请求的是百度地址，则允许跳转
    if ([navigationAction.request.URL.host.lowercaseString isEqual:@"www.baidu.com"]) {
        // 允许跳转
        decisionHandler(WKNavigationActionPolicyAllow);
        return;
    }
    // 否则不允许跳转
    decisionHandler(WKNavigationActionPolicyCancel);
}

// 收到响应后，决定是否允许或取消导航。
- (void)webView:(WKWebView *)webView decidePolicyForNavigationResponse:(WKNavigationResponse *)navigationResponse decisionHandler:(void (^)(WKNavigationResponsePolicy))decisionHandler {
    NSLog(@"%s", __FUNCTION__);
    // 如果响应的地址是百度，则允许跳转
    if ([navigationResponse.response.URL.host.lowercaseString isEqual:@"www.baidu.com"]) {
        // 允许跳转
        decisionHandler(WKNavigationResponsePolicyAllow);
        return;
    }
    // 否则不允许跳转
    decisionHandler(WKNavigationResponsePolicyCancel);
}
```




## 导航策略

* **WKNavigationActionPolicy**
   从 `decisionPolicyForNavigationAction：decisionHandler：`方法返回的决定策略。
   ```objectivec
   typedef enum WKNavigationActionPolicy : NSInteger {
       WKNavigationActionPolicyCancel,
       WKNavigationActionPolicyAllow
   } WKNavigationActionPolicy;
   ```

* **WKNavigationResponsePolicy**
   从 `webView:decidePolicyForNavigationResponse:decisionHandler:`方法返回的决定策略。
   ```objectivec
   typedef enum WKNavigationResponsePolicy : NSInteger {
       WKNavigationResponsePolicyCancel,
       WKNavigationResponsePolicyAllow
   } WKNavigationResponsePolicy;
   ```




# WKUIDelegate API

**WKUIDelegate** 类是网页视图的用户界面委托协议，提供了代表网页呈现本机用户界面元素的方法。

Web视图用户界面委托对象实现此协议来控制打开的新窗口，增加用户单击元素时显示的默认菜单项的行为，并执行其他与用户界面相关的任务。 可以通过处理  **JavaScript** 或其他插件内容来调用这些方法。 默认Web视图实现假定每个Web视图有一个窗口，因此非常规用户界面可能会实现用户界面委托。

参考：[【iOS 开发】WKWebView 学习笔记 (3)－WKUIDelegate](https://www.jianshu.com/p/5f4bba47e323)

## Symbols

### 创建 web 视图

* `- webView:createWebViewWithConfiguration:forNavigationAction:windowFeatures:`
  创建一个新的 web 视图

---

### 显示 UI 面板

* `- webView:runJavaScriptAlertPanelWithMessage:initiatedByFrame:completionHandler:`
  显示一个 JavaScript 警告面板。
* `- webView:runJavaScriptConfirmPanelWithMessage:initiatedByFrame:completionHandler:`
  显示一个 JavaScript 确认面板。
* `- webView:runJavaScriptTextInputPanelWithPrompt:defaultText:initiatedByFrame:completionHandler:`
  显示一个 JavaScript 文本输入面板。

---

### 关闭web视图

* `webViewDidClose:`
  通知应用程序 DOM 窗口成功关闭。

---

### 显示上传面板

* `- webView:runOpenPanelWithParameters:initiatedByFrame:completionHandler:`
  显示文件上传面板。

---

### 响应强制触控动作
* `- webView:shouldPreviewElement:`
  确定给定元素是否应显示预览。
* `- webView:previewingViewControllerForElement:defaultActions:`
  当用户执行窥视操作时调用。
* `- webView:commitPreviewingViewController:`
  当用户在预览中执行弹出操作时调用。

---



### OC 原生显示 JS 弹窗的正确方法

参考： [WKWebView completionHandler called before dismissal @stackoverflow](http://stackoverflow.com/questions/32891812/wkwebview-completionhandler-called-before-dismissal)

```objectivec
// 显示一个 JavaScript 警告弹窗
- (void)webView:(WKWebView *)webView runJavaScriptAlertPanelWithMessage:(NSString *)message initiatedByFrame:(WKFrameInfo *)frame completionHandler:(void (^)(void))completionHandler {
    
    UIAlertController *alertController = [UIAlertController alertControllerWithTitle:@"AlertPanel" message:message preferredStyle:UIAlertControllerStyleAlert];
    
    UIAlertAction *alertAction = [UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleDefault handler:^(UIAlertAction * _Nonnull action) {
        
        // ⚠️必须在这里回调 JavaScript 
        completionHandler();
        
    }];
    [alertController addAction:alertAction];
    [self presentViewController:alertController animated:YES completion:nil];
    
}
```

#### WKUIDelegate 示例代码：
```objectivec
#pragma mark - WKUIDelegate

// 创建新的webView
- (nullable WKWebView *)webView:(WKWebView *)webView createWebViewWithConfiguration:(WKWebViewConfiguration *)configuration forNavigationAction:(WKNavigationAction *)navigationAction windowFeatures:(WKWindowFeatures *)windowFeatures {
    WKFrameInfo *frameInfo = navigationAction.targetFrame;
    if (![frameInfo isMainFrame]) {
        [webView loadRequest:navigationAction.request];
    }
    return nil;
}

// 显示 JavaScript 警告框
// 测试JS代码：alert("alert message!")
- (void)webView:(WKWebView *)webView runJavaScriptAlertPanelWithMessage:(NSString *)message initiatedByFrame:(WKFrameInfo *)frame completionHandler:(void (^)(void))completionHandler {
    [UIAlertController showAlertInViewController:self withTitle:@"温馨提示" message:message cancelButtonTitle:nil destructiveButtonTitle:nil otherButtonTitles:@[@"确认"] tapBlock:^(UIAlertController * _Nonnull controller, UIAlertAction * _Nonnull action, NSInteger buttonIndex) {
        completionHandler();
    }];
}

// 确认框
// 测试JS代码：confirm("confirm message!")
- (void)webView:(WKWebView *)webView runJavaScriptConfirmPanelWithMessage:(NSString *)message initiatedByFrame:(WKFrameInfo *)frame completionHandler:(void (^)(BOOL result))completionHandler {
    [UIAlertController showAlertInViewController:self withTitle:@"需要再次确认" message:message cancelButtonTitle:@"取消" destructiveButtonTitle:nil otherButtonTitles:@[@"确认"] tapBlock:^(UIAlertController * _Nonnull controller, UIAlertAction * _Nonnull action, NSInteger buttonIndex) {
        if (buttonIndex == 0) {
            completionHandler(NO);
        }else {
            completionHandler(YES);
        }
    }];
}
```



# WKScriptMessageHandler API

遵守 **WKScriptMessageHandler** 协议的类，提供了从网页中运行 **JavaScript** 接收消息的方法。



```objectivec
// 从 web 界面中接收到一个脚本时调用
- (void)userContentController:(WKUserContentController *)userContentController didReceiveScriptMessage:(WKScriptMessage *)message;
```



> 这个协议只包含以上一个必须实现的方法，这个方法是提高App与web端交互的关键，它可以直接将接收到的JS脚本转为 OC 或 Swift 对象。（当然，在 **UIWebView** 也可以通过“曲线救国”的方式与web进行交互，著名的**Cordova** 框架就是这种机制）
>
> ——[WKWebView的新特性与使用 @saitjr](http://www.saitjr.com/ios/ios-wkwebview-new-features-and-use.html)



> 这个 API 真正神奇的地方在于 JavaScript 对象可以*自动转换*为 Objective-C 或 Swift 对象。
>
> ——[WKWebView @nshipster](http://nshipster.cn/wkwebkit/) 



> 在 JavaScript 端通过  `window.webkit.messageHandlers.{InjectedName}.postMessage()` 方法来发送消息到native。我们需要遵守该协议，然后实现代理方法，就可以收到消息，并做相应的的处理。
>
> 如果在开始时就注入很多的名称，就需要区分处理
>```objectivec
> if (message.name == "AppModel" ) {
> //...
> }
>```
> ——[WKWebView特性及使用 @360doc](http://www.360doc.com/content/15/1216/20/29321654_520908727.shtml) 【Swift】



# WKUserScript API

**WKUserScript** 对象表示可以注入到网页中的脚本。



## Symbols

### 初始化脚本
* `-initWithSource:injectionTime:forMainFrameOnly:`
  返回可以添加到用户内容控制器中的初始化用户脚本。

---

### 检查脚本信息

* `source`
  脚本源代码
* `injectionTime`
  脚本应该被注入网页中的时间点。
* `forMainFrameOnly`
  布尔值，指示脚本是否仅应注入主框架（是）或所有框架（否）。

---

### 常量

* `WKUserScriptInjectionTime`
  将用户脚本注入网页的时间。

```objectivec
typedef enum WKUserScriptInjectionTime : NSInteger {
    WKUserScriptInjectionTimeAtDocumentStart, // 在文档元素创建之后，但在加载任何其他内容之前注入脚本。
    WKUserScriptInjectionTimeAtDocumentEnd //在文件完成加载后，但在其他子资源完成加载之前注入该脚本。
} WKUserScriptInjectionTime;
```

`WKUserScript` 对象可以以 JavaScript 源码形式初始化，初始化时还可以传入时间是在加载之前还是结束时注入，以及脚本影响的是这个布局还是仅主要布局。于是用户脚本被加入到 `WKUserContentController` 中，并且以 `WKWebViewConfiguration` 属性传入到 `WKWebView` 的初始化过程中。


```objectivec
- (void)viewDidLoad {
    [super viewDidLoad];
  
    // 使用 WKUserScript 注入JavaScript脚本
    // 图片缩放的js代码
    NSString *js = @"var count = document.images.length;for (var i = 0; i < count; i++) {var image = document.images[i];image.style.width=320;};window.alert('找到' + count + '张图');";
    
    // 根据JS字符串初始化 WKUserScript 对象
    WKUserScript *script = [[WKUserScript alloc] initWithSource:js injectionTime:WKUserScriptInjectionTimeAtDocumentEnd forMainFrameOnly:YES];
    
    // 根据生成的 WKUserScript 对象，初始化 WKWebViewConfiguration 对象
    WKWebViewConfiguration *config = [[WKWebViewConfiguration alloc] init];
    [config.userContentController addUserScript:script];
    
    // 初始化 WKWebView 对象
    _webView = [[WKWebView alloc] initWithFrame:self.view.bounds configuration:config];
    _webView.UIDelegate = self;
    _webView.navigationDelegate = self;
    [_webView loadHTMLString:@"<head></head>![](http://upload-images.jianshu.io/upload_images/2648731-54ae5490e32407cf.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)" baseURL:nil];
    [self.view addSubview:_webView];
    
}
```

## 使用示例：自适应文本字体大小
场景：加载的 HTML 网页没有对移动端设备屏幕做适配，导致加载后显示的字体太大或者太小，影响用户体验，通过注入 JavaScript 脚本的方式可以解决这个问题。
以下为代码示例：

```objectivec
- (WKWebViewConfiguration *)webViewConfiguration {
    if (!_webViewConfiguration) {
        _webViewConfiguration = [[WKWebViewConfiguration alloc] init];
        
        // 自适应字体
        NSString *javaScript = @"var meta = document.createElement('meta'); meta.setAttribute('name', 'viewport'); meta.setAttribute('content', 'width=device-width'); document.getElementsByTagName('head')[0].appendChild(meta);";
        WKUserScript *userScript = [[WKUserScript alloc] initWithSource:javaScript injectionTime:WKUserScriptInjectionTimeAtDocumentEnd forMainFrameOnly:YES];
        WKUserContentController *contentController = [[WKUserContentController alloc] init];
        [contentController addUserScript:userScript];
        _webViewConfiguration.userContentController = contentController;
    }
    return _webViewConfiguration;
}
```
