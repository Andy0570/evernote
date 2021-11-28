# 1 零基础小白如何进行 iOS 系统学习

首先，学习目标要明确；
其次，有了目标，要培养兴趣，经常给自己一些正面的反馈，比如对自己的进步进行鼓励，在前期小步快走；
再次，学技术最重要的一点就是多动手。

推荐书目：
* 《Objective-C 教程（第 2 版） 》
* 《Objective-C 基础教程 》
* 《iOS 编程（第四版）》
* 《iOS 编程（第五版）》 
* 《iOS 开发指南》

# 2 基础入门后，如何进行高级进阶

## 2.1 原理和基础：掌握扎实的原理和基础是进阶的必要条件

首先是语言。 **入门时候可能只要对语言看得懂，会写即可。但是在进阶阶段，看懂会写是远远不够的**。

这个时候，编码规范就很重要，我们不能随心所欲的写，推荐书目《Effective Objective-C 2.0》。

除此之外，我们还需熟练掌握 Objective-C Runtime。我推荐的素材有[objc4](http://opensource.apple.com/source/objc4/)，这门语言比较好的是它开源，所以我们可以去看它的源码。第二是苹果的这篇文档[Objective-C Runtime Programming Guide](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtInteracting.html)，我们可以仔细去阅读。从我自身经历来看，对 `objc_msgSend()` 实现细节的理解程度很大程度上反映了我们对 Objective-C Runtime 的掌握程度。再推荐大家一篇文档 The Swift Programming Language，苹果官方的文档，大家可以反反复复去看。

## 2.2 iOS GUI 学习

我听到有些人说 GUI 学习太简单了，我随便看看能刷几个页面就可。还会有这样一种说法，GUI 的代码太难看了。

我个人十分重视GUI，我认为这是真正挑战的地方，因为这能真正反映我们的熟练程度，是否真正懂得其背后的设计原理。 

**APP Guide:**
*  View Programming guide
*  View Controller Programming Guide
*  Event Handling Guide
*  Drawing and Printing Guide
*  Core Animation Programming Guide

## 2.3 Xcode学习

工欲善其事，必先利其器。在我看来，Xcode 就是这个器，因此需要十分重视。

**Apple Guide:**
*  Xcode Overview
*  Xcode Project Management Guide
 
关于Xcode的流程，我们需要清晰的知道Xcode编译、链接、打包的流程，配置和Xcodeconfig的操作。另外，还需知道Xcode管理多工程、多Target. 还需熟练使用cocoapods、plugin。当然，LLVM 调试工具也是必不可少的。

## 2.4 网络

理解 ~~NSUPLConnection~~、NSURLSession、NSURLProtocol

**Apple Guide:**
*  URL Loading System Programming Guide
*  Introduction to CFNetwork Programming Guide

## 2.5 多线程

推荐书目《Objective-C高级编程 iOS 与 OS X 多线程和内存管理》，这本书的特点是透彻，全面。

**Apple Guide:**
*  Concurrency Programming Guide
*  Threading Programming Guide

## 2.6 逆向安全（了解层面）

《iOS应用逆向工程》对我们去了解逆向这些事情和应用的保护是很有帮助的。

**Apple Guide:**
* Keychain Services Programming Guide

## 2.7 学习资源

*  WWDC Session
*  Objc.io
此外，优秀的开源库也值得我们学习，如：
*  SDWebImage
*  AFNetworking/Restkit
*  Facebook的三个库：AsyncDisplayKit/pop/ReactNative
*  ReactiveCocoa

# 3  iOS能力清单

## 3.1 GUI

自定义 UI、熟悉 UI 渲染机制
掌握基本排版机制，掌握 AutoLayout
熟悉事件传递机制，自定义手势
掌握基本动画，动画原理

## 3.2 Controller

熟悉基本 ViewController 使用、transition
熟悉 MVC 等设计模式

## 3.3 熟悉常见网络库使用

熟悉 ~~NSURLConnection~~, NSURLSession, NSURLProtocol, AFNetworking, 熟悉Cache, cookie管理

## 3.4 熟悉 Sqlite 使用，熟悉常见 ORM 系统设计和实现，熟悉系统常用本地存储机制

熟悉 CoreData, sqlite, UserDefault

## 3.5 熟悉多线程消息传递，同步机制，线程池设计和实现

掌握NSOperation  GCD  Runloop 机制和实现

## 3.6 熟悉音频、视频基本概念，熟悉相关系统接口

熟悉拍照、录像等相关接口 

## 3.7 编程语言

熟悉 C/C++、Objective-C、Swift

## 3.8 性能优调

熟悉移动端常见性能问题和解决方案：主线程CPU密集操作，主线程IO操作，排版、渲染耗时            
网络性能分析和调优(《Web 性能权威指南》)
熟练使用 Instrument 进行性能调优

## 3.9 安全

熟悉常见iOS应用安全机制(keychain，codesign)，熟悉常见应用数据保护机制
熟悉常见加解密算法
熟悉 iOS 逆向与安全

# 4 走向专家的知识体系

内功而非招式，更看重基础的掌握。 所以，我们需要加强自身实际的能力，主要包括：
* 数学基础
* 操作系统
* 程序的编译上，链接与加载，推荐《程序员的自我修养》
*  ARM体系架构，推荐《ARM体系结构与编程》
* 学习多范式的编程语言
* 设计模式
* 计算机网络
* 数据库

# 5 企业需要怎样的iOS人才

* 大公司更看重基础，成长性
* 技术热情
* 系统学习的能力
* 听说能力
* 敢于承担、敢于挑战


# 参考

* [iOS学习之路(资料推荐) @没阳光的午后](http://www.jianshu.com/p/d5d97a3b366e) 
* [最佳实践（2）：iOS开发篇](http://ios.jobbole.com/81830/)
