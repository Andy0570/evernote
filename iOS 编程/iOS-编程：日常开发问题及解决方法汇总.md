> 该列表用于记录日常开发中遇到的编译问题和解决方法!

### 1. 编译器错误：Unsupported compiler 'com.apple.compilers.llvmgcc42' selected for architecture 'armv7'

解决方法：
Build Settings - Build Options - Compile for C/C++/Objective-C 选项卡设置为：**Default compiler(Apple LLVM 9.0)**
![](http://upload-images.jianshu.io/upload_images/2648731-67ffa6d420240086.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### 2. Missing .entitlements file 导致代码签名失败无法编译问题
此问题产生的原因是我在项目中 **New Group with Flower** 时无意间对名为  `XXX.entitlements` 的文件进行了移动。
导致 Targets - Build Settings - Signing - Code Signing Entitlements 这里提示找不到 `.entitlements` 的文件。

解决方法：重新设置 `.entitlements` 文件的完整路径。一开始我不知道是哪里有问题，就把 **Code Signing Entitlements**这一栏的值清空了，然后去 TARGETS - General - Signing 中重新勾选 `Automatically manage signing`，Team 一栏空缺的话就重新勾选。然后 Capabilities 中 Push Notifications 如果有⚠️警告之类的提示就点击 fix 按钮。最后也可以解决问题，编译成功。



### 3. EXC_BAD_ACCESS 问题
参考： [EXC_BAD_ACCESS的本质详解以及僵尸模式调试原理](http://www.cocoachina.com/ios/20160226/15324.html)



### 4. Xcode 8、9屏蔽控制台多余的Log日志

Xcode菜单栏 > Product > Scheme > Edit Scheme > Run > Arguments > Environment Variables 设置：
**OS_ACTIVITY_MODE = disable**。

> 💡Tips 
> 设置`DYLD_PRINT_STATISTICS` = `YES` 可以在控制台打印加载时长。 

  参考：[Xcode8 屏蔽控制台多余的Log日志](http://blog.csdn.net/xia0liang/article/details/52923658)



### 5. Xcode升级到8.2.1后编辑区域码字意外退出

 解决方法：查看意外退出报告，Finder前往plug-ins目录，删除相关文件（**KSImageNamed.ideplugin**）。
 ![](http://upload-images.jianshu.io/upload_images/2648731-7726eeee1230ff57.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 参考：[“Xcode”意外退出(无限意外退出) 问题的解决方法](http://www.jianshu.com/p/b20f2ceb8d4d)



### 6. Xcode升级后插件失效错误分析与解决方案

[参考1](http://forkpanda.com) 、[参考2](http://joeshang.github.io/2015/04/10/fix-xcode-upgrade-plugin-invalid/)



### 7. Xcode删除文件后Issue 导航栏missing file警告

  解决方法：运行终端，执行命令行``cd``进入missing file目录，然后运行:
``svn delete nameOfMissingFile``
  参考：[Xcode删除文件后missing file警告 ](http://blog.csdn.net/yuanya/article/details/12494783)

某次删除图片文件显示警告: `a peg revision is not allowed here`
解决方法是加 @ 后缀：
```objective-c
$svn delete main_normal@2x-1.png
svn: E200009: 'main_normal@2x-1.png': a peg revision is not allowed here
$ svn delete main_normal@2x-1.png@
D         main_normal@2x-1.png
```



### 8. 真机调试NSLog无法打印

  解决方法：使用 ``printf()``方法。或者使用DDLog日志框架。
```objective-c
#ifdef DEBUG

#define MYString [NSString stringWithFormat:@"%s", __FILE__].lastPathComponent
#define NSLog(...) printf("%s 第%d行: %s\n\n",[MYString UTF8String] ,__LINE__, [[NSString stringWithFormat:__VA_ARGS__] UTF8String]);

#else
#define NSLog(...)
#endif
```
 参考：[Xcode 8解决真机测试Log被屏蔽的问题](http://www.jianshu.com/p/caaea517ad36)



### 9. 在Storyboard上修改了Text的文字，真机上调试并没有生效的问题

  解决方法：汉化问题，Storyboard下还有一个中文版副本
  参考：[IOS StoryBoard修改对于真机无效的问题](http://www.2cto.com/kf/201308/234274.html)



### 10. 项目编译运行通过，断点在main函数上breakpoint 1.2

解决方法：首先修改添加的All Exception breakpoint:
 ![](http://upload-images.jianshu.io/upload_images/2648731-0498170c359810e0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
再去控制台定位问题。



### 11. 调试打印方法名

```objective-c
// 在控制台打印方法名
NSLog(@"%s",__func__);
NSLog(@"%s",__FUNCTION__);
NSLog(@"%s",__PRETTY_FUNCTION__);
NSLog(@"%@",NSStringFromSelector(_cmd));
```



### 12. 自动布局约束发生歧义

解决方法：使用 `_autolayoutTrance` 方法
① 在视图控制器的 `viewWillAppear:` 方法中设置一个断点。
② 当程序在断点处停下来之后，在控制台输入：
```
(lldb) po [[UIWindow keyWindow] _autolayoutTrance]
```
如果应用界面与期望的方式不一致，同时也无法确定问题的原因，就可以使用该方法找出有歧义布局的视图。



### 13 设置严格选择器匹配

Build Settings:

-Wstrict-selector-match

### 14 [iOS 解决 CUICatalog: Invalid asset name supplied 问题](https://blog.csdn.net/qq_19979539/article/details/52493194)

![](https://upload-images.jianshu.io/upload_images/2648731-536438bda8218b5e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 编译器错误：directory not found for option
####  查询 Library 报错

错误内容：`"directory not found for option '-L/..."`

解决方法：Project -> targets -> Build Setting -> Library Search Paths 删除该路径。


#### 查询 Framework 报错

错误内容：`"directory not found for option '-F/..."`

解决方法：Project -> targets -> Build Setting -> Framework Search Paths 删除该路径。

## 参考

* [多年iOS开发经验总结(一) @杂雾无尘](https://www.jianshu.com/p/1ff9e44ccc78)
* [多年iOS开发经验总结(二) @杂雾无尘](https://www.jianshu.com/p/9fcd37c0ea05)
* [iOS - 常见问题的整理 (一) @重庆妹子在霾都](https://www.jianshu.com/p/15b91b260d16)
* [iOS - 常见问题的整理 (二) @重庆妹子在霾都](https://www.jianshu.com/p/aa07ae3be254)

