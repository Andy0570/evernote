## WKWebView 新特性

![](http://upload-images.jianshu.io/upload_images/2648731-f744429511ac6994.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 加载 web 页面

### 加载本地资源

* `- loadHTMLString:baseURL:`
  同步方式加载本地资源，数据可以来源于本地文件或者硬编码的 HTML 字符串

```objectivec
// 设定主页文件的基本路径，通过一个HTML字符串加载主页数据
- (IBAction)loadHTMLString:(id)sender {
    
    // 主页文件名
    NSString *htmlPath = [[NSBundle mainBundle] pathForResource:@"Baidu001"
                 ofType:@"html"];
    // 主页文件的基本路径
    NSURL *bundleUrl = [NSURL fileURLWithPath:[[NSBundle mainBundle] bundlePath]];
    
  	// 将 index.html 文件的内容读取到 NSString 对象中
    NSError *error = nil;  
    NSString *html = [[NSString alloc] initWithContentsOfFile:htmlPath
                                                     encoding:NSUTF8StringEncoding
                                                        error:&error];
    //数据加载没有错误的情况下
    if (error == nil) {
        [self.webView loadHTMLString:html baseURL:bundleUrl];
    } 
}
```

* `- loadData:MIMEType:characterEncodingName:baseURL:`
  指定 MIME 类型、编码集和 `NSDate` 对象加载一个主页数据，并设定主页文件基本路径

```objectivec
// NSData 是一种二进制的字节数组类型。它没有字符集的概念，但用它来装载 webView 的时候必须指定字符集。
- (IBAction)loadDATA:(id)sender {
    
    NSString *htmlPath = [[NSBundle mainBundle] pathForResource:@"index"
                 ofType:@"html"];
    NSData *htmlData = [[NSData alloc] initWithContentsOfFile:htmlPath];
  
    NSURL *bundleUrl = [NSURL fileURLWithPath:[[NSBundle mainBundle] bundlePath]];
    
    [self.webView loadData:htmlData
                  MIMEType:@"text/html"
          textEncodingName:@"UTF-8"
                   baseURL:bundleUrl];
    
}
```



> 1. 加载本地HTML：iOS9以上的系统可以使用 `WKWebView loadFileURL:allowingReadAccessToURL:`，iOS9以下的版本没有这个方法，需要先将本地HTML文件的复制到tmp目录中，然后使用 `loadRequest:` 方法来加载。如果在HTML中引入其他资源文件，例如js，css，image等必须一同复制到temp目录中
>
>    ——[WKWebview开发笔记 @Qin's Blog](https://www.oneqy.com/2016/07/28/WKWebViewDevNote/) 



### 加载网络资源

```objectivec
// 创建 WKWebView 对象
CGRect rect = CGRectMake(0, 0, self.view.width, self.view.height);
WKWebView *webView = [[WKWebView alloc] initWithFrame:rect];
// 设置导航代理，监听网页加载进程
_webView.navigationDelegate = self;
// 将 webView 对象添加到视图
[self.view addSubview:webView];
// 根据URL发起网络请求
NSURLRequest *request = [NSURLRequest requestWithURL:[NSURL URLWithString:@"www.google.com"]];
[webView loadRequest:request];
```



## 创建可与 JavaScript 交互的 WKWebView

### 1. Objective-C 调用 JavaScript 方法
* JS 方法：
  ```javascript
  <!-- JavaScript 方法， 接收一个参数 --> 
  function alertAction(message) {
  	alert(message);
  }
  ```

* Objective-C 原生调用
  ```objectivec
  // OC 调用 JS 的方法
  [self.mainWebView evaluateJavaScript:@"alertAction('OC调用JS方法时传入的参数')" completionHandler:^(id _Nullable item, NSError * _Nullable error) {
      NSLog(@"alert");
  }];
  ```

### 2. JavaScript 调用 Objective-C 方法

参考：[WKWebView的使用和各种坑的解决方法（OC＋Swift）](http://www.jianshu.com/p/403853b63537)

* 第一步：
```objectivec
- (WKWebViewConfiguration *)webViewConfiguration {
    if (!_webViewConfiguration) {
        
        // 创建 WKWebViewConfiguration 对象
        _webViewConfiguration = [[WKWebViewConfiguration alloc] init];
        
        // 创建 WKUserContentController 对象，提供 JavaScript 向 webView 发送消息的方法
        WKUserContentController *userContentColtroller = [[WKUserContentController alloc] init];
        // 添加消息处理，注意：self指代的对象需要遵守 WKScriptMessageHandler 协议，结束时需要移除
        [userContentColtroller addScriptMessageHandler:self name:@"close"];
        // 将 userConttentController 设置到配置文件
        _webViewConfiguration.userContentController = userContentColtroller;
        
    }
    return _webViewConfiguration;
}
```

💡 ScriptMessageHandler 的 **name** 必须和 web 中的 **JavaScript** 方法名称一致:
`window.webkit.messageHandlers.close.postMessage("message");`

```javascript
<script type="text/javascript">

    function doPrint() {
        if (checkiOS() == true) {
            // iOS 注入代码
            window.webkit.messageHandlers.scriptName.postMessage("script message！");
        } else {
            // Android 注入代码
            Android.startLHYL();
        }
    }

    // Android/iOS设备判断
    function checkiOS() {

        var u = navigator.userAgent;

        var isAndroid = u.indexOf('Android') > -1 || u.indexOf('Adr') > -1; // Android
        var isiOS = !!u.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/);             // iOS

        if (isiOS) {
            return true;
        } else {
            return false;
        }
    }

</script>
```


* 第二步：

```objective-c
#pragma mark - WKScriptMessageHandler

- (void)userContentController:(WKUserContentController *)userContentController didReceiveScriptMessage:(WKScriptMessage *)message {

    NSLog(@"%s",__FUNCTION__);
    
    if ([message.name isEqualToString:@"close"]) {
        
        NSLog(@"WKScriptMessage:%@",message.body);
    }
}
```

* 第三步：

```objectivec
- (void)dealloc { 
  
    // 移除 ScriptMessageHandler
    [self.webView.configuration.userContentController removeScriptMessageHandlerForName:@"close"];
}
```



##  显示加载进度条

> 对于 **WKWebView** ，有三个属性支持 KVO，因此我们可以监听其值的变化，分别是：`loading`、`title`、`estimatedProgress`,对应功能表示为：是否正在加载中、页面的标题、页面内容加载进度（值为0.0~1.0）



刚开始用的方法：[WKWebView添加进度条](http://www.jianshu.com/p/85ae953380f9/comments/4803365#comment-4803365) ，参照着写完发现如果网速好的话，会出现进度条走不完就被隐藏的现象。
解决方法：添加延时animation动画，参考：[iOS WKWebView添加网页加载进度条](http://www.jianshu.com/p/d3c8ba672760)

代码如下：
```objectivec
#import "OSCViewController.h"
#import <WebKit/WebKit.h>

@interface OSCViewController () <WKNavigationDelegate>

@property (nonatomic, strong) NSURL *URL;
@property (nonatomic, strong) WKWebViewConfiguration *webViewConfiguration;
@property (nonatomic, strong) WKWebView *webView;

/** 1️⃣ 添加 progressView 属性*/
@property (nonatomic, strong) UIProgressView *progressView;

@end

@implementation OSCViewController

#pragma mark - Lifecycle

- (void)loadView {
    [super loadView];
    self.view = self.webView;
}

- (void)viewDidLoad {
    [super viewDidLoad];
    // 3️⃣ 将 progressView 添加到父视图
    [self.view addSubview:self.progressView];

    // 4️⃣ 使用 KVO 注册观察者
    // 监听 WKWebView 对象的 estimatedProgress 属性，就是当前网页加载的进度
    [self.webView addObserver:self
                   forKeyPath:@"estimatedProgress"
                      options:NSKeyValueObservingOptionNew
                      context:nil];
    
    NSURLRequest *request = [NSURLRequest requestWithURL:self.URL];
    [self.webView loadRequest:request];
}

- (void)dealloc {
    // 6️⃣ 移除观察者
    [self.view removeObserver:self forKeyPath:@"estimatedProgress"];

}
#pragma mark - Custom Accessors

- (NSURL *)URL {
    if (!_URL) {
        _URL = [NSURL URLWithString:@"http://www.oschina.net/"];
    }
    return _URL;
}

- (WKWebViewConfiguration *)webViewConfiguration {
    if (!_webViewConfiguration) {
        _webViewConfiguration = [[WKWebViewConfiguration alloc] init];
    }
    return _webViewConfiguration;
}

- (WKWebView *)webView {
    if (!_webView) {
        _webView = [[WKWebView alloc] initWithFrame:[[UIScreen mainScreen] bounds] configuration:self.webViewConfiguration];
        // 设置导航代理，监听网页加载进程
        _webView.navigationDelegate = self;
    }
    return _webView;
}

/**
 2️⃣ 初始化progressView

 @return 返回初始化后的进度条视图
 */
- (UIProgressView *)progressView {
    if (!_progressView) {
        CGRect sreenBounds = [[UIScreen mainScreen] bounds];
        CGRect progressViewFrame = CGRectMake(0, 64, sreenBounds.size.width, 1);
        _progressView = [[UIProgressView alloc] initWithFrame:progressViewFrame];
        // 设置进度条色调
        _progressView.tintColor = [UIColor blueColor];
        // 设置进度条跟踪色调
        _progressView.trackTintColor = [UIColor whiteColor];
    }
    return _progressView;
}

#pragma mark - KVO

// 5️⃣ 接收变更后的通知，计算 webView 的进度条
- (void)observeValueForKeyPath:(NSString *)keyPath
                      ofObject:(id)object
                        change:(NSDictionary<NSKeyValueChangeKey,id> *)change
                       context:(void *)context {
    
    if ([keyPath isEqualToString:@"estimatedProgress"]) {

        self.progressView.progress = self.webView.estimatedProgress;
        
        if (self.progressView.progress == 1) {
            /*
             * 添加一个简单的动画，将 progressView 的 Height 变为1.5倍
             * 动画时长0.25s，延时0.3s后开始动画
             * 动画结束后将 progressView 隐藏
             */
            __weak __typeof(self)weakSelf = self;
            [UIView animateWithDuration:0.25f delay:0.3f options:UIViewAnimationOptionCurveEaseInOut animations:^{
                weakSelf.progressView.transform = CGAffineTransformMakeScale(1.0f, 1.5f);
            } completion:^(BOOL finished) {
                weakSelf.progressView.hidden = YES;
            }];
        }
        
    }else {
        [super observeValueForKeyPath:keyPath
                             ofObject:object
                               change:change
                              context:context];
    }
}

#pragma mark - WKNavigationDelegate

//  页面开始加载web内容时调用
- (void)webView:(WKWebView *)webView didStartProvisionalNavigation:(WKNavigation *)navigation {
    self.progressView.hidden = NO;
    // 防止 progressView 被网页挡住
    [self.view bringSubviewToFront:self.progressView];
}

//  当web内容开始返回时调用
- (void)webView:(WKWebView *)webView didCommitNavigation:(WKNavigation *)navigation {
    
}

//  页面加载完成之后调用
- (void)webView:(WKWebView *)webView didFinishNavigation:(WKNavigation *)navigation {
    
}

//  页面加载失败时调用 ( 【web视图加载内容时】发生错误)
- (void)webView:(WKWebView *)webView didFailProvisionalNavigation:(WKNavigation *)navigation withError:(NSError *)error {
    NSLog(@"Error:%@",error.localizedDescription);
    self.progressView.hidden = YES;
}

// 【web视图导航过程中发生错误】时调用。
- (void)webView:(WKWebView *)webView didFailNavigation:(WKNavigation *)navigation withError:(NSError *)error {
    NSLog(@"Error:%@",error.localizedDescription);
    self.progressView.hidden = YES;

    // 如果请求被取消
    if (error.code == NSURLErrorCancelled) {
        return;
    }
}

// 当Web视图的Web内容进程终止时调用。
- (void)webViewWebContentProcessDidTerminate:(WKWebView *)webView {
    self.progressView.hidden = YES;
}

@end
```


### 监听 webView 的 title 属性以动态设置标题

```objectivec
- (void)viewDidLoad {
    [super viewDidLoad];
        
    // 添加观察者，监听 WKWebView 对象的 title 属性
    [self.webView addObserver:self forKeyPath:@"title" options:NSKeyValueObservingOptionNew context:NULL];
    
    NSURLRequest *request = [NSURLRequest requestWithURL:self.URL];
    [self.webView loadRequest:request];
}

- (void)dealloc {
    // 移除观察者
    [self.view removeObserver:self forKeyPath:@"title"];
}

#pragma mark - KVO

- (void)observeValueForKeyPath:(NSString *)keyPath
                      ofObject:(id)object
                        change:(NSDictionary<NSKeyValueChangeKey,id> *)change
                       context:(void *)context {
    
     if ([keyPath isEqualToString:@"title"]) {
        self.title = change[@"new"];
    }else {
        [super observeValueForKeyPath:keyPath
                             ofObject:object
                               change:change
                              context:context];
    }
}
```



### URL 中文处理

```objectivec
- (NSURL *)URL {
    if (!_URL) {
#pragma clang diagnostic push
#pragma clang diagnostic ignored"-Wdeprecated-declarations"
        _URL = [NSURL URLWithString:(NSString *)CFBridgingRelease(CFURLCreateStringByAddingPercentEscapes(kCFAllocatorDefault, (CFStringRef)@"https://www.oschina.net/ios/home", (CFStringRef)@"!$&'()*+,-./:;=?@_~%#[]", NULL,kCFStringEncodingUTF8))];
#pragma clang diagnostic pop
    }
    return _URL;
}
```



### 导航栏右侧添加刷新按钮

```objectivec
#pragma mark - Lifecycle

- (void)viewDidLoad {
    [super viewDidLoad];
    
    // 刷新按钮
    UIBarButtonItem *rightBarButtonItem = [[UIBarButtonItem alloc] initWithBarButtonSystemItem:UIBarButtonSystemItemRefresh target:self action:@selector(rightBarButtonDidClicked:)];
    self.navigationItem.rightBarButtonItem = rightBarButtonItem;
}

#pragma mark - IBActions

- (void)rightBarButtonDidClicked:(id)sender {
    [self.webView reload];
}
```

### 禁用长按选中文字效果
![https://segmentfault.com/q/1010000006649503](https://upload-images.jianshu.io/upload_images/2648731-623df890f50eec86.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


```objectivec
- (void)webView:(WKWebView *)webView didFinishNavigation:(null_unspecified WKNavigation *)navigation {
    
    // 禁用选中效果
    [self.webView evaluateJavaScript:@"document.documentElement.style.webkitUserSelect='none'" completionHandler:nil];
    [self.webView evaluateJavaScript:@"document.documentElement.style.webkitTouchCallout='none'" completionHandler:nil];
}
```


## 加载 HTML 图片大小自适应

参考 [WebView加载HTML图片大小自适应与文章自动换行](http://www.brighttj.com/ios/ios-webview-load-html-image-adaptive.html)

在 HTML 代码中设置内容样式，一般使用 **css** 或者 **js** ，根据加载优先级以及加载效果，可以自行选择。

- js在页面加载完之后加载，所以设置图片样式的时候，会先加载大图，然后突然变小；
- css在引入时加载，直接加载缩小的图片（实际占用内存不会缩小）；

### 一：使用 CSS 进行图片的自适应

```objectivec
#define HTML_NO_HEAD @"![](http://upload-images.jianshu.io/upload_images/2648731-a7ddbb6f227eb44d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)<br/>![](http://upload-images.jianshu.io/upload_images/2648731-f4855a77d3daa14f.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)<br/>"

#define HTML_HAD_HEAD @"<head></head><body>![](http://upload-images.jianshu.io/upload_images/2648731-a7ddbb6f227eb44d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)<br/>![](http://upload-images.jianshu.io/upload_images/2648731-f4855a77d3daa14f.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)<br/></body>"

- (void)viewDidLoad {
    [super viewDidLoad];
    
    // 没有<head>标签
//    [self.webView loadHTMLString:[self reSizeImageWithHTMLNoHead:HTML_NO_HEAD] baseURL:nil];
    
    // 有<head>标签
    [self.webView loadHTMLString:[self reSizeImageWithHTMLHadHead:HTML_HAD_HEAD] baseURL:nil];
}

/	----------------------------------------------------------------
// 1.图片样式：不管用户以前设置的图片尺寸是多大，都缩放到宽度为320px大小。

// 如果后台返回的HTML代码中，不包含 <head> 标签，则可以直接在HTML字符串前拼接代码。
- (NSString *)reSizeImageWithHTMLNoHead:(NSString *)html {
    
    return [NSString stringWithFormat:@"<head><style>img{width:320px !important;}</style></head>%@", html];
}

// 如果包含 <head> 标签，则在<head>标签内部替换添加
- (NSString *)reSizeImageWithHTMLHadHead:(NSString *)html {
    
    return [HTML_HAD_HEAD stringByReplacingOccurrencesOfString:@"<head>" withString:@"<head><style>img{width:320px !important;}</style>"];
}

/	----------------------------------------------------------------
// 2.图片样式：若需要根据图片原本大小，宽度小于320px的不缩放，大于320px的缩小到320px，那么在HTML字符串前加上一下代码：
- (NSString *)reSizeImageWithHTML:(NSString *)html {
    
    return [NSString stringWithFormat:@"<head><style>img{max-width:320px !important;}</style></head>%@", html];
}
```



###  二：使用 JavaScript 进行图片的自适应

在 webview 的代理中，执行 JavaScript 代码。(下面这段代码是必须有 `<head>` 标签的)

如果没有 `<head>` 标签，也很简单，只需要给返回的 HTML 字符串前面拼接一个 `<head></head>` 即可。

```objectivec
- (void)webViewDidFinishLoad:(UIWebView *)webView
 {
    [webView stringByEvaluatingJavaScriptFromString:
     @"var script = document.createElement('script');"
     "script.type = 'text/javascript';"
     "script.text = \"function ResizeImages() { "
         "var myimg,oldwidth,oldheight;"
         "var maxwidth=320;"// 图片宽度
         "for(i=0;i <document.images.length;i++){" "myimg = document.images[i];" "if(myimg.width > maxwidth){"
                 "myimg.width = maxwidth;"
             "}"
         "}"
     "}\";"
     "document.getElementsByTagName('head')[0].appendChild(script);"];
    [webView stringByEvaluatingJavaScriptFromString:@"ResizeImages();"];
}
```



## 文章内容自动换行

文章的自动换行也是通过css实现的，书写方式图片缩放类似。在没有<body>标签的情况下，在HTML代码前，直接拼接以下代码即可（若包含<body>，则将代码添加到body标签内部），意思是全部内容自动换行。

```css
<body width=320px style=\"word-wrap:break-word; font-family:Arial\">
```





### 参考

* [WKWebview 使用攻略](https://mp.weixin.qq.com/s/Coz_7ScivDVKv2Iloz0zWw?scene=25#wechat_redirect)
* [WKWebViewTips](https://github.com/ShingoFukuyama/WKWebViewTips)
* [浅谈 WKWebView 使用、JS 的交互](https://www.jianshu.com/p/48a34b20fcd1?utm_source=desktop&utm_medium=timeline)
* [iOS 新闻类 App 内容页技术探索](https://dequan1331.github.io/)
* [WKWebView 在实际开发中的使用汇总](https://www.jianshu.com/p/5dab90e2e5f1) 【URL中文处理、监听estimatedProgress、title、H5通过原生方法调用相册】
* [WKWebView @nshipster](http://nshipster.cn/wkwebkit/) 【 Swift】
* [WKWebView的新特性与使用 @saitjr](http://www.saitjr.com/ios/ios-wkwebview-new-features-and-use.html)
* [使用safari对webview进行调试 @saitjr](http://www.brighttj.com/ios/ios-user-safari-debug-webview.html)
* [WebView加载HTML图片大小自适应与文章自动换行 @saitjr](http://www.brighttj.com/ios/ios-webview-load-html-image-adaptive.html) 
* [~~还在用UIWebView?何不试试WKWebView @吴白~~](http://www.jianshu.com/p/7d17cca71741)
* [WKWebview开发笔记 @Qin's Blog](https://www.oneqy.com/2016/07/28/WKWebViewDevNote/) 【清理缓存？】
* [WKWebView特性及使用 @360doc](http://www.360doc.com/content/15/1216/20/29321654_520908727.shtml) 【Swift】
* [带你走进WKWebView的世界 @SOI](http://www.jianshu.com/p/39401c9e5ea3)
* [WKWebView的使用和各种坑的解决方法（OC＋Swift）](http://www.jianshu.com/p/403853b63537)
* [js(javascript)与ios(Objective-C)相互通信交互  @天狐博客](http://www.skyfox.org/javascript-ios-navive-message.html)
* [WKWebView 使用及注意点(keng) @伯乐在线](http://ios.jobbole.com/90729/)
* [ShingoFukuyama /**WKWebViewTips**](https://github.com/ShingoFukuyama/WKWebViewTips/blob/master/README.md)
* [IOS进阶之WKWebView @ 翻滚的牛宝宝 ](http://www.jianshu.com/p/4fa8c4eb1316)
* [~~iOS8 WebKit库之——WKWebView篇~~](http://blog.csdn.net/cyforce/article/details/37657009)
* [iOS WebViewJavascriptBridge简单运用方法 @iSuAbner](http://www.jianshu.com/p/c5f6e0fc9eaf)
* [iOS下JS与OC互相调用（六）--WKWebView + WebViewJavascriptBridge](http://www.jianshu.com/p/e951af9e5e74)
* [WK 与 JS 的那些事](https://www.jianshu.com/p/c9ceb6a824e2)
* [API翻译：UIWebView （Swift版）](http://www.jianshu.com/p/bc935fab4e30)
* [API翻译：WebKit](http://www.jianshu.com/p/18d2f56b8414)
* [API翻译：WKWebView （Swift版）](http://www.jianshu.com/p/793d815006c5)
