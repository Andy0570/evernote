应用程序编程接口(API)参考文档

[UIWebView Offical API reference](https://developer.apple.com/reference/uikit/uiwebview)   
[WKWebView Offical API reference](https://developer.apple.com/reference/webkit/wkwebview)


# UIWebView
你可以使用 **UIWebView** 类来在你的APP中嵌入Web内容。要做到这一点，创建一个**UIWebView**对象，将其连接到窗口，并向它发送加载网页内容的请求。你也可以使用这个类在网页访问历史中前进或后退，你甚至可以通过编程设置一些网页内容属性。

# 预览

> 注   
> 以 iOS 8 或者以后的版本运行的应用程序，使用``WKWebView``类而不是使用``UIWebView``类.此外，考虑到如果你渲染的文件不支持运行JavaScript，那么应该将``WKPreferences``属性中的``javaScriptEnabled``设置为False。      

---

> 重要   
> 被连接的 iOS 应用程序或者 iOS 10.0 之后必须在其Info.plist文件中包含它需要访问的数据类型的使用说明键，否则将导致崩溃。特别地，如需访问用户的照片数据，它必须还包括``NSPhotoLibraryUsageDescription`` 和 ``NSCameraUsageDescription``。   

附：[允许http协议请求数据解决方案](http://www.jianshu.com/p/257a28846956)
[关于iOS9中的App Transport Security相关说明及适配](http://www.jianshu.com/p/908b8f4c7ece)


使用``loadHTMLString（_：BASEURL:)``方法开始加载本地HTML文件或``loadRequest（_:)``方法开始加载网页内容。使用``stopLoading（）``方法停止加载，此外使用``isLoading``属性查看Web视图是否在加载过程中。

如果要允许用户通过网页访问历史前进或后退页面，那么你可以使用``GoBack的（）``和``goForward（）``方法作为按钮的动作。使用``canGoBack``和``canGoForward``属性以禁用按钮,使用户无法在一个方向上移动。

默认情况下，Web视图会自动将出现在网页内容中的电话号码转换为电话链接。当电话链接被点击时，电话应用程序会启动并拨打该号码。要关闭此默认行为，需要设置``UIDataDetectorTypes``属性的``phoneNumberdataDetectorTypes``位域中不包含``phoneNumber``标志。   

您也可以以编程方式使用``scalesPageToFit``属性来设置Web视图首次显示在网页上的比例。此后，用户可以通过手势改变比例。

如果你想跟踪网页内容的加载，需要设置对象的``delegate``属性使其遵守``UIWebViewDelegate``协议，

> 重要   
> 你不应该在``UIScrollView``的对象中嵌入``UIWebView``或``UITableView``的对象。如果这样做，可能会导致意外的行为，因为这两个对象的触摸事件可能被混合并错误处理。

你可以在``UIWebView``中使用 Web 检查器轻松地调试HTML，CSS，和JavaScript内容。阅读 Debugging Web Content on iOS 以了解如何配置适用于iOS的Web检查。阅读 Safari Web Content Guide 的剩余部分以了解如何创建一个在iPhone和iPad上用于优化Safari浏览器的网页内容。

有关基本视图行为的信息，请参阅 View Programming Guide for iOS。

# 支持的文件格式
除了HTML内容，``UIWebView``的对象可以被用于显示其他内容类型，如Keynote，PDF和网页文档。但是，为了最好的渲染简单而丰富的文本，你应该使用的``UITextView``来代替。

# 保存状态
在iOS 6 及之后的版本中，如果你对视图的``restorationidentifier``属性赋值，它会试图保持该视图的URL历史，每个页面的缩放和滚动位置，以及当前正在查看的页面的信息。在恢复过程中，视图会恢复这些值使网页内容看起来就像以前那样。有关状态保存和恢复如何工作的详细信息，请参阅 App Programming Guide for iOS。

有关外观和行为的配置的详细信息，请参阅Web Views。


# 子类注意事项
``UIWebView``类不能被继承。

#关键字
## 设置代理
*	var delegate:   
	Object-C：``@property(nonatomic, assign) id< UIWebViewDelegate > delegate``   
	接收者的代理
	
## 加载内容
*	func load(Data, mimeType: String, textEncodingName: String, baseURL: URL)    
	设置主页面内容，MIME类型，编码方式和URL基地址。
	
*	func loadHTMLString(String, baseURL: URL?)   
	设置主页内容和URL基地址。

*	func loadRequest(URLRequest)    
	通过启动一个异步客户端请求连接到一个给定的网址.

*	var request: URLRequest?    
	URL请求确认需要加载内容的位置

*	var isLoading: Bool
	布尔值，该值指示接收器是否完成加载内容。
	
*	func stopLoading()    
	停止加载由接收器管理的任何Web内容。

*	func reload()
	重新加载当前页面。
	
## 前进和后退
*	var canGoBack: Bool   
	布尔值，该值指示接收器是否可以向后回退

*	var canGoForward: Bool   
	布尔值，该值指示接收器是否可以向前移动
	
*	func goBack()    
	加载前向列表中的前一个位置   

*	func goForward()   
	加载前向列表中的后一个位置
	
## 设置网页内容属性
*	var allowsLinkPreview: Bool   
	布尔值，该值确定是否按一个链接可以显示目标链接的预览

*	var scalesPageToFit: Bool   
	布尔值，确定是否网页的大小适应视图，并且用户可以改变页面大小

*	var scrollView: UIScrollView   
	与web视图关联的滚动视图

*	var suppressesIncrementalRendering: Bool   
	布尔值，该值指示Web视图是否抑制内容渲染，直到它完全加载到内存中为止

*	var keyboardDisplayRequiresUserAction: Bool   
	布尔值，指示是否可以用编程的方式在Web内容中显示键盘
	
*	var dataDetectorTypes: UIDataDetectorTypes   
	在Web视图中，将数据类型转换为可点击的URL。

## 运行Javascript
*	func stringByEvaluatingJavaScript(from: String)   
	返回运行JavaScript脚本的结果。虽然这种方法并未被废除，最好的做法是使用``wkwebview``类的``evaluatejavascript（_：completionhandler：）``方法代替。

## 管理媒体播放
*	var allowsInlineMediaPlayback: Bool   
	布尔值，确定是否使用在线HTML5视频播放或使用本地全屏幕控制器

*	var mediaPlaybackRequiresUserAction: Bool    
	布尔值，确定HTML5视频是否可以自动播放或需要用户开始播放它们
  
*	var mediaPlaybackAllowsAirPlay: Bool   
	布尔值，确定是否允许在这个视图中使用Air Play
   
*	var allowsPictureInPictureMediaPlayback: Bool    
	布尔值，确定是否在该视图中允许播放图片

## 管理页面
*	var gapBetweenPages: CGFloat   
	页面之间点的间隙大小

*	var pageCount: Int   
	通过Web视图的布局产生的页数
  
*	var pageLength: CGFloat   
	页面流的方向上，每一页的大小
*	var paginationBreakingMode: UIWebPaginationBreakingMode    
	列或分页发生的方式

*	var paginationMode: UIWebPaginationMode    
	web视图中的内容布局

## 常数
*	UIWebViewNavigationType    
	常数指示用户的动作。

*	UIWebPaginationBreakingMode   
	列或分页发生的方式
	
*	UIWebPaginationMode    
	web视图中的内容布局，它决定了页面流的方向。

# 关系
### 继承自： UIView

### 遵守的协议： 
* CVarArg
* Equatable
* Hashable
* NSCoding
* UIAccessibilityIdentification
* UIScrollViewDelegate

