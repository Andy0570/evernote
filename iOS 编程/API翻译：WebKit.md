应用编程接口(API)参考文档

框架
# WebKit
在窗口中显示网页内容。实现浏览器功能，例如展示由用户发起的链接，管理前向列表，并管理最近访问过的历史页面。

# 概述
**WebKit** 提供了一组类，在窗口中显示网页内容，并实现了浏览器的功能，比如展示被用户点击的链接，管理前向列表，并管理最近访问过的历史页面。**WebKit** 大大简化了加载网页的复杂度，更确切地说，以随机的顺序，从响应逐渐递增，或部分由于网络错误的HTTP服务器上异步请求网页内容。它还简化了显示内容可以包含MIME（多用途的网际邮件扩充协议）类型的过程，和拥有各自滚动条的复合框架元素。

> 并行注解   
> **WebKit** 框架并不是线程安全的。如果您需要在该框架中调用函数或方法，那么只可以在主程序线程上这样做。

# 关键字 #

##　创建和配置网页视图
* WKNavigationDelegate   
	WkNavigationDelegate 协议的方法帮助你实现在Web视图被接收，加载，完成导航请求的过程中被触发的自定义行为。

*   WKProcessPool   
	WKProcessPool 对象表示Web内容进程池。

*	WKWindowFeatures   
	当一个新的web视图被请求时，WKWindowFeatures 对象指定窗口包含的可选属性。

*	WKWebView   
	WkWebView 对象显示交互式Web内容，例如一个内置应用程序的浏览器。

*	WKWebViewConfiguration      
	WKWebViewConfiguration 对象是一个用来初始化Web视图属性的集合。

*	WKPreferences   
	WKPreferences 对象为Web视图封装偏好设置。

*	WKUIDelegate   
	WKUIDelegate 类提供了展示代表网页的本地用户界面元素的方法。

## web视图导航 ## 	
*	WKNavigation   
	WKNavigation 对象包含用于跟踪网页加载进度的信息。

*	WKNavigationAction   
	WKNavigationAction 对象包含一个动作可能导致用于决策的导航信息。

*	WKNavigationResponse   
	WKNavigationResponse 对象包含用于决策的导航响应信息。

## 遍历反馈链表 ##
*	WKBackForwardList   
	WKBackForwardList 对象是一个之前访问的可以前进或后退的web视图的网页列表。

*	WKBackForwardListItem      
	WKBackForwardListItem 对象代表了在web视图的前向列表中的网页。

## 访问元素和框架信息 ##
*	WKFrameInfo   
	WKFrameInfo 对象包含了一个网页的框架信息。

## 查询和删除网站数据 ## 
*	WKWebsiteDataRecord   
	WKWebsiteDataRecord 对象代表了由源网址的域名和后缀构成的网站数据。

*	WKWebsiteDataStore   
	WKWebsiteDataStore 对象代表了被选中网站的各种使用数据。数据类型包括cookie数据，磁盘数据和缓存数据，以及如WebSQL，IndexedDB数据库和本地存储等持久性数据。

## 上传文件 ##
*	WKOpenPanelParameters 【Beta】 

## 合并脚本 ##
*	 WKUserContentController   
	 WKUserContentController 对象为JavaScript语言向web视图中发送消息和注入用户脚本提供方法。

*	WKScriptMessage   
	WKScriptMessage 对象包含了从网页上发送来的信息。

*	WKUserScript   
	WKUserScript 对象代表一个可以被注入网页的脚本

*	WKScriptMessageHandler   
	符合 WKScriptMessageHandler 协议的类提供了一个方法接收来自以JavaScript语言运行的网页消息。

## 第一方网页识别 ##
*	WKSecurityOrigin   
	WKSecurityOrigin 对象由主机名称，协议，和端口号组成。第一方负载是以相同的安全原点作为请求的网站的任何负载网址(URL,统一资源定位地址)。第一方网页可以访问对方的资源，如脚本和数据库。
	
## 预览项目
*	WKPreviewElementInfo 【Beta】

*	WKPreviewActionItem 【Beta】   
	wkpreviewactionitem 协议提供接入预览动作项目的属性。

## WebKit剩余API
*	Document Object Models API (Legacy)
*	Setting Up a Web View (Legacy)
*	Accessing Previous Webpages (Legacy)
*	Archiving Webpages (Legacy)
*	Loading Resources (Legacy)
*	Working with Frames (legacy)
*	Downloading Information (Legacy)
*	Opening a File (Legacy)
*	Setting Policies (Legacy)
*	Implementing Plugins (Legacy)
*	Incorporating Scripts (Legacy)
*	Working With Document Web Views (Legacy)

## 其它参考文档
*	WebKit Structures
*	WebKit Enumerations
*	WebKit Functions
*	WebKit Data Types
*	WebKit Constants

## 扩展类型
*	NSObject   
	NSObject 类是大多数 Object-C 语言类层次结构的根类。通过 NSObject 类，对象可以继承运行系统的基本接口并且具有表现得像 Object-C 对象一样的能力。
	
### 测试版软件
本文档包含关于API或技术开发的初步信息。本信息可能随时更改，且根据本文档实现的软件应与最终操作系统软件进行测试。

