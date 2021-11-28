应用程序编程接口(API)参考文档

类
# WKWebView
**WKWebView** 对象显示交互式Web内容，例如例如应用内浏览器。

# 预览
对于新的开发，使用该类而不是老的 **UIWebView** 类。

# 关键字
### 初始化web视图

*	init(frame: CGRect, configuration: WKWebViewConfiguration)   
	返回一个以特定的框架和配置初始化的web视图
	

<!-- more -->

## 检查web视图信息

*	var configuration: WKWebViewConfiguration   
	初始化web视图的配置副本

*	var scrollView: UIScrollView   
	与web视图关联的滚动视图

*	var title: String?   
	页面标题

*	var url: URL?   
	有效的URL地址

*	var customUserAgent: String?   
	如果没有自定义已设置的用户代理字符串，设置用户代理为字符串或者nil

*	~~var certificateChain: [Any]~~ 【失效】  
	当前已提交的导航的证书链的对象数组

## 设置协议
*	var navigationDelegate: WKNavigationDelegate?    
	web视图的导航委托

*	var uiDelegate: WKUIDelegate?    
	web视图的用户界面委托

## 加载内容
*	var estimatedProgress: Double   
	预计当前已加载的导航为哪一块

*	var hasOnlySecureContent: Bool   
	布尔值，该值指示该页上的所有资源是否已通过安全加密的连接加载

*	func loadHTMLString(String, baseURL: URL?)   
	设置网页内容和URL地址

*	var isLoading: Bool   
	布尔值，该值指示视图是否正确加载内容

*	func reload()   
	重新加载当前页面

*	func reloadFromOrigin()   
	重新载入当前页面，如果可能的话，使用缓存验证条件进行端到端的验证

*	func stopLoading()   
	停止装载当前页面上的所有资源

*	func load(Data, mimeType: String, characterEncodingName: String, baseURL: URL)   
	设置Web页面内容和URL地址

*	func loadFileURL(URL, allowingReadAccessTo: URL)   
	导航到所请求的文件系统中的文件URL地址

## 缩放内容
*	var allowsMagnification: Bool   
	布尔值，指示放大手势是否会改变Web视图放大倍数。

*	var magnification: CGFloat   
	当前页面内容的缩放因子

*	func setMagnification(CGFloat, centeredAt: CGPoint)   
	通过指定的因子缩放页面内容，并将结果集中在指定的点上

## 导航
*	var allowsBackForwardNavigationGestures: Bool   
	布尔值，指示是否水平滑动手势可以出发前向列表导航。

*	var backForwardList: WKBackForwardList   
	Web视图的前向列表

*	var canGoBack: Bool   
	布尔值，指示是否可以导航到前向列表中的后退项中

*	var canGoForward: Bool   
	布尔值，指示是否可以导航到前向列表中的前进项中

*	var allowsLinkPreview: Bool   
	布尔值，该值确定是否按一个链接可以显示链接目标的预览

*	func goBack()   
	在前向列表中导航到后退项中

*	func goForward()   
	在前向列表中导航到前进项中

*	func go(to: WKBackForwardListItem)   
	从前向列表中导航到某一项并将该项设置为当前项目

*	func load(URLRequest)   
	导航到请求的URL地址

## 执行JavaScript语言
*	func evaluateJavaScript(String, completionHandler: ( (Any?, Error?) -> Void)? = nil)   
	评估JavaScript字符串

## 发送界面生成器动作消息
下面的动作方法是由界面生成器触发用于响应用户事件的。

*	func reload(Any?)   
	重新加载当前页

*	func reloadFromOrigin(Any?)   
	重新加载当前页, 如果可能的话，使用缓存验证条件执行端到端验证。

*	func stopLoading(Any?)   
	停止在当前页上加载所有资源

*	func goBack(Any?)   
	在前向列表中导航到后退项中

*	func goForward(Any?)    
	在前向列表中导航到前进项中
##	初始化
*	init?(coder: NSCoder) 【Beta】

##	实例属性
*	var serverTrust: SecTrust? 【Beta】

# 关系
### 继承自： NSView, UIView

### 遵守的协议： 
*	CVarArg
*	Equatable
*	Hashable
*	NSUserInterfaceValidations
*	UIAccessibilityIdentification

### 测试版软件
本文档包含关于API或技术开发的初步信息。本信息可能随时更改，且根据本文档实现的软件应与最终操作系统软件进行测试。

