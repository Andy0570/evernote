> 视图控制器导航栏

### 参考

- [GitHub 源码：shinobicontrols/iOS7-day-by-day](https://github.com/ShinobiControls/iOS7-day-by-day)
- [天天品尝iOS7甜点 :: Day 20 :: View controller content and navigation bars](http://blog.kingiol.com/2014/01/25/ios7-day-by-day-day20-view-controller-content-and-navigation-bars/)

## 视图控制器在导航控制器中

### edgesForExtendedLayout 属性：

扩展的边缘布局。该属性仅适用于嵌入到容器中（例如 `UINavigationController`）的视图控制器。窗口的根视图控制器不对此属性做出反应。默认值为 `UIRectEdgeAll`。

#### 1. 默认值 UIRectEdgeAll

```objective-c
UIRectEdgeAll = UIRectEdgeTop | UIRectEdgeLeft | UIRectEdgeBottom | UIRectEdgeRight
```

默认值 `UIRectEdgeAll` 在 Interface Builder 中的设置：

![](http://upload-images.jianshu.io/upload_images/2648731-b380ea97e79c142d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在视图控制器中添加子视图：

```objective-c
UIView *box = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 200, 200)];
box.backgroundColor = [UIColor blueColor];
[self.view addSubview:box];
```

显示效果：

![](http://upload-images.jianshu.io/upload_images/2648731-38cd3b09817ee033.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 2. 正确设置 UIRectEdgeNone

代码设置：```self.edgesForExtendedLayout = UIRectEdgeNone;```

Interface Builder 设置：

![](http://upload-images.jianshu.io/upload_images/2648731-17a859fc06e7edbc.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

显示效果：

![](http://upload-images.jianshu.io/upload_images/2648731-16cb1a5599a5eb30.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)





## 滚动视图在导航控制器中

### automaticallyAdjustsScrollViewInset 属性（iOS 11 deprecate 😱）：

一个布尔值，指示视图控制器是否自动调整其滚动视图的插入内容。

此属性的默认值为YES，这使容器视图控制器知道他们应该调整此视图控制器视图的滚动视图插入，以解决状态栏，搜索栏，导航栏，工具栏或选项卡栏所占用的屏幕区域。 如果您的视图控制器实现管理其自己的滚动视图插入调整，请将此属性设置为NO。

#### 设置为 NO：

![](http://upload-images.jianshu.io/upload_images/2648731-c5a410075bc3de92.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

效果： 滚动视图会嵌入到导航栏下方，顶部第一个 cell 被遮挡，无法正常显示。

当然，现在很多 App 的页面顶部的图片有这种效果。

![](http://upload-images.jianshu.io/upload_images/2648731-cf6b2ffd762fd9e2.gif?imageMogr2/auto-orient/strip)



#### 设置为 YES（默认）

![](http://upload-images.jianshu.io/upload_images/2648731-86e9cddb4779e56c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

效果，正常显示 cell：

![](http://upload-images.jianshu.io/upload_images/2648731-7ecab88065ca0253.gif?imageMogr2/auto-orient/strip)



## 表格视图在导航控制器中

`UITableView`是`UIScrollView`的子类，所以我们期望和上一个滚动视图相同的效果，用到了同样的属性：

### automaticallyAdjustsScrollViewInsets

同样，如果设置为 **NO**，TableView 的上下都会被遮挡：

![](http://upload-images.jianshu.io/upload_images/2648731-f0ac4f485ecceee6.gif?imageMogr2/auto-orient/strip)



而如果设置为 **YES**，则上下都能正常显示：

![](http://upload-images.jianshu.io/upload_images/2648731-7894d69d6910938c.gif?imageMogr2/auto-orient/strip)



## 另外的一些情形：滚动视图或者列表视图嵌入在 UITabBarController 中

这种情况就算设置 `automaticallyAdjustsScrollViewInset = YES` 也于事无补，底部那一块还是会被遮住。

![](http://upload-images.jianshu.io/upload_images/2648731-cf6f7cd462f88084.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](http://upload-images.jianshu.io/upload_images/2648731-c411daf842943745.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



只需要取消勾选 `Under Bottom Bars` 这个属性就好：

![](http://upload-images.jianshu.io/upload_images/2648731-ee9fe82f4a8f873c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> The End.
