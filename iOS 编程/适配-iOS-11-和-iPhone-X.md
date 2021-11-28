## 官方资源
##### [WWDC 2017 Session 204: Updating Your App for iOS 11](https://developer.apple.com/videos/play/wwdc2017/204/)
- [你可能需要为你的APP适配iOS11](http://www.jianshu.com/p/370d82ba3939)
- [简书App适配iOS 11](http://www.jianshu.com/p/26fc39135c34)
- [iOS11 & iPhone X 适配指南](http://www.10tiao.com/html/216/201709/2652552758/2.html)

##### [WWDC视频: Designing for iPhone X](https://developer.apple.com/videos/play/fall2017/801/)
##### [为 iPhone X 更新您的 app](https://developer.apple.com/cn/ios/update-apps-for-iphone-x/?from=timeline&amp;isappinstalled=0)
##### [Apple: Layout Guides and Safe Area](https://developer.apple.com/ios/human-interface-guidelines/visual-design/adaptivity-and-layout/#layout-guides-and-safe-area)

## 博客资源

- [GitHub: iOS11适配系列教程](https://github.com/ChenYilong/iOS11AdaptationTips)
- [iOS 11 安全区域适配总结](http://www.jianshu.com/p/efbc8619d56b)
* [适配iOS11，适配iPhoneX，适配安全区的几个文章和宏](http://www.10tiao.com/html/216/201709/2652552870/3.html)
* [美团点评：iPhone X 刘海打理指北](https://tech.meituan.com/iPhoneX刘海打理指北.html)
* [贝聊科技:贝聊 iPhone X 适配实战](http://blog.csdn.net/fzhlee/article/details/78455339)
* [手机管家iPhoneX的适配总结](http://wetest.qq.com/lab/view/337.html?from=content_SegmentFault)
* [随手记在iPhone X上的适配实践总结](https://mp.weixin.qq.com/s/vzSUc2YPVIRD8CmkxaDfxg)
* [iOS屏幕适配系列(一): Autoresizing技术](http://www.jianshu.com/p/12cfdcf05651)



##### [关于iOS11中estimatedRowHeight](http://www.cocoachina.com/ios/20171109/21103.html)

> 1. `estimatedRowHeight` 是一个预估高度，iOS 11 之前为 0，在 iOS11 下默认为44。
> 2. 使用 `estimatedRowHeight` 属性，可以将系统计算 contentSize 的高度的几何计算成本从加载时推迟到滚动时再执行。
> 3. 结论：当 cell 的实际高度大于预估高度的时候，会按照预估高度下的 cell 的数量来计算 contentSize ，当 cell 的实际高度小于预估高度的时候，会按照实际高度下的 cell 的数量来计算 contentSize。
> 4. 使用 **MJRefresh**：（以下的问题在最新的版本已经修复了！）
>   为什么使用 MJRefresh 在 iOS 11 下要设置 `estimatedRowHeight=0`?
>   因为 MJRefresh 底部的上拉刷新是根据 contentSize 来计算的，当数据更新的时候，得出来的 contentSize 只是预估的。
> 5. 将 `estimatedRowHeight` 的值设置为 0，可以禁用此属性：
```
override func viewDidLoad() { 
tableView.estimatedRowHeight = 0 
tableView.estimatedSectionHeaderHeight = 0 
tableView.estimatedSectionFooterHeight = 0
}
```

##### [天天品尝iOS7甜点 :: Day 20 :: View controller content and navigation bars](http://www.jianshu.com/p/83c4e69f15f8)
```
// iOS 11 中，`automaticallyAdjustsScrollViewInset` 属性被弃用了。
@property(nonatomic, assign) BOOL automaticallyAdjustsScrollViewInsets;
```
> 该属性早年的作用：   
> 默认为 YES，它允许容器视图控制器知道它应该调整插入此视图控制器视图中的滚动视图，以考虑状态栏，搜索栏，导航栏，工具栏或选项卡栏消耗的屏幕区域。





### [WWDC 204: Updating Your App for iOS 11](https://developer.apple.com/videos/play/wwdc2017/204/) 笔记



* **UIBarItem**

横屏模式下，Tabbar 上的图标会变小，长按可以弹出显示大图标。

```objective-c
UIBarItem.landscapeImagePhone
UIBarItem.LargeContentSizeImaage
```



* 控制导航栏显示大标题

主视图控制器上显示大图标

```objective-c
// 设置导航栏显示大标题
navigationBar.prefersLargeTitles = true
```

详细视图控制器只显示小图标

```objective-c
// 详细视图控制器中显示正常标题
navigationItem.largeTitleDisplayMode = .never
```



* 导航栏自动集成 **UISearchController**

```objective-c
navigationItem.searchController = searchController
navigationItem.hidesSearchBarWhenScrolling = YES;
```



* **UIToolbar** 和 **UINavigationBar** 扩展了新的自动布局支持

自定义的bar button items、自定义的 title 都可以通过 layout 来自定义尺寸。

> Extensive new Auto Layout support
>
> Keep your constraints inside your views
>
> “We assume you know what you’re doing”



* 避免零尺寸的自定义视图

> UINavigationBar and UIToolbar provide position 
>
> You must provide size
>
> * Constraints for width and height 
> * Implement `intrinsicContentSize `
> * Connect your subviews via constraints



* Margins and Insets

`margins` 指的是【控件显示内容的边缘】和【控件本身边缘】之间的距离。

![image](http://upload-images.jianshu.io/upload_images/2648731-0a22393ddc275284.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image](http://upload-images.jianshu.io/upload_images/2648731-12a09f6b1ed8e518.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

			![image](http://upload-images.jianshu.io/upload_images/2648731-7cf346bc724b242a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
		
	![image](http://upload-images.jianshu.io/upload_images/2648731-85c7c8f23017d2db.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```objective-c
// systemMinimumLayoutMargins
viewRespectsSystemMinimumLayoutMargins = true // default
// directionalLayoutMargins
 
// edgesForExtendedLayout.top
// topLayoutGuide ❌
// edgesForExtendedLayout.bottom
// bottomLayoutGuide ❌
```



* Safe Area

> Describes area of view not occluded  by ancestors ——用于描述不被遮挡的视图区域
>
> Available as insets or layout guide —— 可以用作插入量或者布局指引
>
> Incorporates overscan compensation insets ——

![image](http://upload-images.jianshu.io/upload_images/2648731-4466bd62c90c7caf.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image](http://upload-images.jianshu.io/upload_images/2648731-2785e8077b15d761.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



* Extending the Safe Area Insets——扩展安全区域插入量

```objective-c
// 通过 addtionalSafeAreaInsets 来改变 safeAreaInsets 的值
UIViewController.additionalSafeAreaInsets

// 获取改变的回调：
UIView.safeAreaInsetsDidChange()
UIViewController.viewSafeAreaInsetsDidChange()
```

### 适配代码

```objective-c
// 设置 view.top = self.view.top
CGFloat top;
if (@available(iOS 11.0, *)) {
    top = self.view.layoutMarginsGuide.layoutFrame.origin.y;
} else {
    top = 64;
}

// Masonry 框架
// 设置 View.top = self.view.top + 60
if (@available(iOS 11.0, *)) {
    make.top.equalTo(self.view.mas_safeAreaLayoutGuideTop).with.offset(0);
} else {
    make.top.equalTo(self.view.mas_top).with.offset(64);
}
```


* Scroll View

`adjustedContentInset`

```objective-c
typedef struct UIEdgeInsets {
    CGFloat top, left, bottom, right;  // 指定每个边的插入量（正数）。 值可能是负的（超出边缘）。
} UIEdgeInsets;
```

![](http://upload-images.jianshu.io/upload_images/927233-4e94d87701df2971.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



* Table View——在iOS 11中默认启用Self-Sizing

`UITableViewAutomaticDimension`

```objective-c
// 关闭 estimateRowHeight 属性
self.tableView.estimatedRowHeight = 0;
self.tableView.estimatedSectionHeaderHeight = 0;
self.tableView.estimatedSectionFooterHeight = 0;

tableView.separatorInsetReference = .fromCellEdges // default
```
