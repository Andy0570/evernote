# 使用webView加载本地HTML代码和网络资源
## Demo演示：
![webViewSample](http://upload-images.jianshu.io/upload_images/2648731-db709b715c08b748.gif?imageMogr2/auto-orient/strip)

### 定义相关控件并关联

	#import <UIKit/UIKit.h>
	@interface ViewController : UIViewController <UIWebViewDelegate>

	//定义输出口webView
	@property (nonatomic,weak) IBOutlet UIWebView *webView;

	//定义动作按钮
	- (IBAction)loadHTMLString:(id)sender;
	- (IBAction)loadDATA:(id)sender;

	- (IBAction)loadRequest:(id)sender;
	
	@end

## 加载本地资源
同步方式加载本地资源，数据可以来源于本地文件或者硬编码的HTML字符串
### 方法一：-(void)loadHTMLString:(NSString *)string baseURL:(nullable NSURL *)baseURL;

	//设定主页文件的基本路径，通过一个HTML字符串加载主页数据
	- (IBAction)loadHTMLString:(id)sender {
    
    NSString *htmlPath = [[NSBundle mainBundle] pathForResource:@"Baidu001"
                 ofType:@"html"];
    //主页文件的基本路径
    NSURL *bundleUrl = [NSURL fileURLWithPath:[[NSBundle mainBundle] bundlePath]];
    
    NSError *error = nil;
    
    //将index.html文件的内容读取到NSString对象中
    NSString *html = [[NSString alloc]
                      initWithContentsOfFile:htmlPath
                      encoding:NSUTF8StringEncoding
                      error:&error];
    
    if (error == nil) { //数据加载没有错误的情况下
        [self.webView loadHTMLString:html baseURL:bundleUrl];
    } 
	}


### 方法二：-(void)loadData:(NSData *)data MIMEType:(NSString *)MIMEType textEncodingName:(NSString *)textEncodingName baseURL:(NSURL *)baseURL;

	//指定MIME类型、编码集和NSDate对象加载一个主页数据，并设定主页文件基本路径
	//NSData是一种二进制的字节数组类型。它没有字符集的概念，但用它来装载webView的时候必须指定字符集。
	- (IBAction)loadDATA:(id)sender {
    
    NSString *htmlPath = [[NSBundle mainBundle] pathForResource:@"index"
                 ofType:@"html"];
    NSURL *bundleUrl = [NSURL fileURLWithPath:[[NSBundle mainBundle] bundlePath]];
    NSError *error = nil;
    
    NSData *htmlData = [[NSData alloc] initWithContentsOfFile:htmlPath];
    
    if (error == nil) { //数据加载没有错误的情况下
        [self.webView loadData:htmlData
                      MIMEType:@"text/html"
              textEncodingName:@"UTF-8"
                       baseURL:bundleUrl];
    }
	}

## 加载网络资源
异步方式加载网络资源，异步调用不会造成主线程堵塞，并且会获得较好的用户体验
### 方法：- (void)loadRequest:(NSURLRequest *)request;


	//提供NSURLRequest对象，需要实现相应的UIWebViewDelegate委托协议
	- (IBAction)loadRequest:(id)sender {
    
	//    NSURL *url = [NSURL URLWithString:@"http:www.51work6.com"];
    NSURL *url = [NSURL URLWithString:@"http://112.64.46.80/tem/"];
 
    NSURLRequest *request = [NSURLRequest requestWithURL:url];
    
    [self.webView loadRequest:request];
    self.webView.scalesPageToFit = YES;
    self.webView.delegate = self;
    
	}
	
	#pragma mark --UIWebViewDelegate委托定义方法
	- (void) webViewDidFinishLoad:(UIWebView *)webView {
    //stringByEvaluatingJavaScriptFromString：方法调用JavaScript语句
    //document.body.innerHTML：获得页面中HTML代码的JavaScript语句
    NSLog(@"%@",[webView stringByEvaluatingJavaScriptFromString:@"document.body.innerHTML"]);
    
	}


## UIWebViewDelegate委托定义方法

 UIWebViewDelegate委托协议定义的方法：
 
* 该方法在webView**开始加载**新的界面**之前**调用，可以用来捕获webView中的JavaScript事件。
	 ``- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType;``
 
* 该方法在webView**开始加载**新的界面**之后**调用。
 	``- (void)webViewDidStartLoad:(UIWebView *)webView;``
 
* 该方法在webView**完成加载**新的界面**之后**调用。
 	``- (void)webViewDidFinishLoad:(UIWebView *)webView;``
 
* 该方法在webView**加载失败**时调用。
 	``- (void)webView:(UIWebView *)webView didFailLoadWithError:(nullable NSError *)error;``
