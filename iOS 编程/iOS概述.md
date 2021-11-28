### iOS系统概述与介绍

**iOS**是一款由苹果公司开发的移动操作系统（OS：Operating System）。苹果公司最早于2007年1月9日的Macworld大会上公布该系统。 
**操作系统**：直接运行在硬件上的最基本的系统软件。  
**iOS开发**：开发运行在iOS系统上的应用或者游戏软件。                                                                                                                                                                             

#### iOS开发归于移动开发领域
* Windows XP、Win7等 是 PC 操作系统
* iOS 是手持设备(iPhone/iPod touch/iPad等 )操作系统

#### iOS开发的三种方式
iOS设备开发的软件根据实现方法分为三大类。
* iPhone本地应用程序，也就是使用iPhone SDK 开发的，在iOS设备上本地运行的程序。
* 仅在网页上显示的内容，包括Web应用等，也就是像内置iPhone应用程序一样运行的网站。
* 混合应用程序，在Web视图区域中提供网页内容的访问，但是也包含了一些iOS用户界面元素。

#### iOS开发基本流程
确定需求 —> 设计交互稿 —>设计视觉稿 —>编写代码 <—>提交测试 —>提交AppStore审核 —> 版本上线

### iOS系统架构
**Core OS** <-> **Core Services** <-> **Media** <-> **Cocoa Touch**

#### Cocoa Touch层
Cocoa Touch层提供了基本的系统行为支持，而且将你的工作量降到最低，当你想要实现更为复杂的行为和界面时，才考虑向下层探寻技术支持。

* UIKit 框架：UIKit提供了一些程序运行所必须的关键对象，使得APP能够捕获用户输入和在屏幕上的显示内容。
* MapKit 框架：iOS 3.0引入该框架，该框架提供一个可被嵌入到应用程序的地图界面，该界面包含一个可以滚动的地图视图。
* Address Book UI 框架：IOS 3.0引入该框架，可以利用框架撰写电子邮件，并将其放入到用户的发件箱排队等候发送
* Message UI 框架：显示创建或者编辑联系人的标准系统界面。

#### Media层
包含图形、音频、视频等技术。

* Quartz Core 框架：包含Core Animation接口。Core Animation是高级动画制作和混合技术，它使用经过优化的渲染路径实现复杂的动画和视觉效果
* Media Player 框架：应用程序播放视频和音频内容。
* AV Foundation 框架：该框架包含的Object—C类可用于播放音频内容。
* Core Graphics 框架（CoreGraphics.framework）包含Quartz 2D绘图API接口。该框架基于C接口，提供绘画功能。

#### Core Service层
应用程序提供基础系统服务。可能应用程序并不直接使用这些服务，但它们是系统很多部分赖以建构的基础。

* Foundation 框架：为Core foundation 框架的许多功能提供Object-C封装。
* Core foundation 框架：是一组C语言接口，它们为IOS应用程序提供基本数据管理和服务功能。
* Core Location 框架：可用于定位某个设备当前经纬度。
* 其他框架：CFNetwork 框架、Core Data 框架、Core Media 框架、Core Telephon y框架、Event Kit 框架、Mobile Core Services 框架、Quick Look 框架、Store Kit 框架、System configuration 框架。

### 操作系统 
硬件 <——> **操作系统** <——> 应用软件 <——> 用户  
操作系统可以看做用户与硬件的接口、桥梁，它为应用软件和用户提供了控制访问硬件的手段。

###iOS&Android的区别
操作系统 | iOS | Android 
----------- | ------------- | ------------ 
系统架构 | 基于UNIX  | 基于Linux的虚拟机
系统安全性 | 安全、稳定  | 安全性略低，漏洞较多
是否开源 | 不开源 | 开源
公司 | Apple | Google
应用商店 | AppStore（08年上线) | GooglePlay
设备种类 | iPhone/iPad等	 | 数不清的设备种类（屏幕、系统适配难度大）

#### 出色手机软件的两点要素
* 良好的用户体验与美观的用户界面
* 实用的功能
 
#### 开发准备
1. 编程语言：C语言、C++（可选）、Objective-C、Swift
2. 开发工具：Xcode
3. 电脑系统：Mac OS X (iMac、MacBook、Mac mini)
4. 真机设备：iPhone、iPad等（可选）
5. iOS证书：调试证书（可选）、发布证书（可选）（$99/year）

#### Apple设备
* iPhone1（2007年发布），iPhone4（2007年）Retina显示屏，支持前置摄像头
* iPad（2010）
* 其他 iPod Touch、Apple TV等

##### 经典广告
> If you don't have an iPhone, well, you don't have an iPhone.  


### Mac OS X
* X代表罗马数字10；  
* 该系统以UNIX系统为基础开发，故所有文件存放在一个根目录中。

#### 桌面
* DOC：桌面应用程序池
* Finder：   我的电脑  
* Launchpad： 应用程序
* Safari： 浏览器
* AppStore： 应用商店
* spotlight： 搜索 （快捷键：Ctrl+space）
* 动态化顶部菜单栏
* 快速预览功能：选中文件后按space

#### 软件下载
1. App Store
2. 第三方网站  
   * [MacX](http://soft.macx.cn)    
   * [applex](http://www.applex.net/downloads/)  
   * [Mac党](http://www.macdang.com)
