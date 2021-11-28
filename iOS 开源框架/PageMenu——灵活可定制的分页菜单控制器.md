* **GitHub 地址：**[PageMenu](https://github.com/uacaps/PageMenu)
* **star:** 5.1k

⚠️ 该库已不再维护！

> 🎉 🎉 🎉  Surprise ！ I'm back！ 本期给大家带来的第三方框架是一个分页菜单控制器——**PageMenu**。该框架同时支持 Swift 和 Objective-C，使用起来也是极其方便，下面一起来跟我看看吧!

## 描述

一个完全可定制、非常灵活的分页菜单控制器，由位于滚动视图内的其他视图控制器构建，允许用户在任何类型的视图控制器之间切换，触发方式类似于Spotify，Windows Phone 和 Instagram 的中的轻击或滑动手势效果。

**Similar to Spotify**

![](http://upload-images.jianshu.io/upload_images/2648731-dd661ce1c7073fcc.gif?imageMogr2/auto-orient/strip)

![PageMenuScreen2](https://upload-images.jianshu.io/upload_images/2648731-66d219f53003adc3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**Similar to Windows Phone**

![](https://upload-images.jianshu.io/upload_images/2648731-53b10869d296e9f2.gif?imageMogr2/auto-orient/strip)

![PageMenuScreen2](https://upload-images.jianshu.io/upload_images/2648731-400f836ab2a07dc3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**Similar to Instagram segmented control**

![](http://upload-images.jianshu.io/upload_images/2648731-c4baabe9fe16ffd1.gif?imageMogr2/auto-orient/strip)

![PageMenuDemoScreen6](https://upload-images.jianshu.io/upload_images/2648731-dc7940864662275f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 安装

### CocoaPods

PageMenu 支持通过 [CocoaPods](http://cocoapods.org) 安装，⚠️ 此方式仅支持 **Swift** 语言。

将以下代码添加到你的 `Podfile` 文件中即可安装:
```bash
pod 'PageMenu'
```

### Carthage

PageMenu 也支持通过 [Carthage](https://github.com/carthage/carthage) 安装。  添加以下代码到 Cartfile 并且按照 [该指示](https://github.com/carthage/carthage#adding-frameworks-to-an-application) 执行.

```bash
github "uacaps/PageMenu"
```

### 手动安装

用于 PageMenu 所需的类文件位于此项目根目录的 `Classes` 目录下：

* CAPSPageMenu.swift

## 如何使用 PageMenu

首先，你必须创建一个视图控制器，该控件要作为页面菜单的容器。 该视图控制器可以是一个有独立 xib 文件、并将其 xib 文件放在 Storyboard 中的视图控制器。 在此之后，您将通过下面列出的几个简单的步骤，以便让所有的功能正常运行。

### 1. 将安装部分中列出的文件添加到项目中

### 2. 在容器视图控制器中添加 `CAPSPageMenu` 属性

Swift

```swift
var pageMenu : CAPSPageMenu?
```

Objective-C

```objectivec
@property (nonatomic) CAPSPageMenu *pagemenu;
```

### 3. 在视图控制器的 `viewDidLoad` 方法中添加初始化代码

Swift

```swift
// Array to keep track of controllers in page menu
var controllerArray : [UIViewController] = []

// Create variables for all view controllers you want to put in the 
// page menu, initialize them, and add each to the controller array. 
// (Can be any UIViewController subclass)
// Make sure the title property of all view controllers is set
// Example:
var controller : UIViewController = UIViewController(nibName: "controllerNibName", bundle: nil)
controller.title = "SAMPLE TITLE"
controllerArray.append(controller)

// Customize page menu to your liking (optional) or use default settings by sending nil for 'options' in the init
// Example:
var parameters: [CAPSPageMenuOption] = [
    .MenuItemSeparatorWidth(4.3), 
    .UseMenuLikeSegmentedControl(true), 
    .MenuItemSeparatorPercentageHeight(0.1)
]

// Initialize page menu with controller array, frame, and optional parameters
pageMenu = CAPSPageMenu(viewControllers: controllerArray, frame: CGRectMake(0.0, 0.0, self.view.frame.width, self.view.frame.height), pageMenuOptions: parameters)

// Lastly add page menu as subview of base view controller view
// or use pageMenu controller in you view hierachy as desired
self.view.addSubview(pageMenu!.view)
```

Objective-C

```objectivec
// 1. 该数组用于存放 PageMenu 所有需要包含的视图控制器
NSMutableArray *controllerArray = [NSMutableArray array];

// 2. 创建并初始化你想要放进 PageMenu 中的所有视图控制器的实例对象，并将其添加到控制器数组中。
//（可以是任何 UIViewController 的子类对象）
// 请确保设置所有视图控制器的标题属性。
// 示例代码:
UIViewController *controller = [UIViewController alloc] initWithNibname:@"controllerNibnName" bundle:nil];
controller.title = @"SAMPLE TITLE";
[controllerArray addObject:controller];

// 3. PageMenu 偏好设置，将所有参数封装到一个 NSDictionary 字典中
// 根据你的喜好（可选）自定义页面菜单，或在初始化时通过发送 nil 给 'options' 选项使用默认设置
// 示例代码:
NSDictionary *parameters = @{CAPSPageMenuOptionMenuItemSeparatorWidth: @(4.3),
                             CAPSPageMenuOptionUseMenuLikeSegmentedControl: @(YES),
                             CAPSPageMenuOptionMenuItemSeparatorPercentageHeight: @(0.1)
                             };

// 4. 通过控制器数组、尺寸和可选参数初始化 PageMenu
_pageMenu = [[CAPSPageMenu alloc] initWithViewControllers:controllerArray frame:CGRectMake(0.0, 0.0, self.view.frame.size.width, self.view.frame.size.height) options:parameters];

// 5. 最后，将 PageMenu 作为容器视图控制器的子视图添加进来
// 或根据需要将 pageMenu 控制器添加到视图的层次结构中，
[self.view addSubview:_pageMenu.view];
```

### 4.  可选 - 协议方法

如果需要使用委托方法，首先将 PageMenu 的委托设置为父视图控制器（就是让容器视图控制器遵守并实现 PageMenu 的委托协议 ）。

Swift

```swift
// Optional delegate 
pageMenu!.delegate = self
```

Objective-C

```objectivec
// 可选委托协议 
_pageMenu.delegate = self;
```

之后，你就可以在父视图控制器中实现以下委托方法：

Swift

```swift
func willMoveToPage(controller: UIViewController, index: Int){}

func didMoveToPage(controller: UIViewController, index: Int){}
```

Objective-C

```objectivec
// 可选委托协议 
- (void)willMoveToPage:(UIViewController *)controller index:(NSInteger)index {}

- (void)didMoveToPage:(UIViewController *)controller index:(NSInteger)index {}
```

### 5.  🎉 你现在应该已经会使用 PageMenu 了!!

## 自定义设置

你可以按照需求用各种方法来自定义 PageMenu。并且在不久的将来，更多的定制方法也会被实现以确保 PageMenu 适应你的 APP 设计。这些都将是 **CAPSPageMenu** 在你的容器视图控制器中可修改的属性。

以下是每个类别提供的属性：

### 1)  颜色

*   Page Menu 滚动视图后面的背景色可融合到视图控制器背景中（Scroller View 的背景色）
    ```objectivec
    viewBackgroundColor (UIColor)
    ```
* 滚动菜单背景颜色。默认值：`[UIColor blackColor]`
    ```objectivec
    scrollMenuBackgroundColor (UIColor)
    ```
*   选择指示器的颜色。默认值：`[UIColor whiteColor]`
     ```objectivec
     selectionIndicatorColor (UIColor)
     ```
*   当前选中的菜单项的标签颜色。默认值：`[UIColor whiteColor]`
     ```objectivec
     selectedMenuItemLabelColor (UIColor)
     ```
*   未选中的菜单项标签颜色。默认为 `[UIColor lightGrayColor]`
     ```objectivec
     unselectedMenuItemLabelColor (UIColor)
     ```
*   菜单项分隔符颜色（用于分段控件样式）。默认为 `[UIColor lightGrayColor]`  🚫 测了半天也没看见分隔符在哪 😂
     ```objectivec
     menuItemSeparatorColor (UIColor)
     ```
*   菜单底部分割线颜色（页面菜单与视图之间的水平分割线）。默认值：`[UIColor whiteColor]`
     ```objectivec
     bottomMenuHairlineColor (UIColor)
     ```

### 2. Dimensions（尺寸）

*   滚动菜单高度，默认值：34.0
     ```objectivec
     menuHeight (CGFloat)
     ```
*   滚动菜单边距（第一个菜单项之前的距离，以及最后一个菜单项之后的距离以及菜单项之间的距离）默认值：15.0
     ```objectivec
     menuMargin (CGFloat)
     ```
*   滚动菜单项宽度，默认值：111.0
  ```objectivec
  menuItemWidth (CGFloat)
  ```
*   选择指示器高度，默认值：3.0
  ```objectivec
  selectionIndicatorHeight (CGFloat)
  ```

### 3. Segmented Control（分段控制器）

*   将 **PageMenu** 作为分段控件使用。默认值：`NO`
     ```objectivec
     useMenuLikeSegmentedControl (Bool)
     ```
* 菜单项分隔符宽度（以像素为单位）
  ```objectivec
  menuItemSeparatorWidth (CGFloat)
  ```
*   菜单项分隔符高度，以菜单高度的百分比表示。默认值：0.5
     ```objectivec
     menuItemSeparatorPercentageHeight (CGFloat)
     ```
*   菜单项分隔符设置圆角。默认值：`NO`
     ```objectivec
     menuItemSeparatorRoundEdges (Bool)
     ```


### 4. 其他设置

*   菜单项标题文本字体：默认值：`[UIFont systemFontOfSize:15.0]`
    ```objectivec
    menuItemFont (UIFont)
    ```
*   添加菜单底部分割线。默认值：`YES`
     ```objectivec
     addBottomMenuHairline (Bool)
     ```
*   菜单项宽度基于标题文字宽度（标题有多宽，菜单项就有多宽）。默认值：`NO`
     ```objectivec
     menuItemWidthBasedOnTitleTextWidth (Bool)
     ```
*   禁用/启用滚动视图控制器的水平反弹。默认值：`YES`
     ```objectivec
     enableHorizontalBounce (Bool)
     ```
*   隐藏/取消隐藏顶部页面菜单栏。默认值：`NO`
     ```objectivec
     hideTopMenuBar (Bool)
     ```
*   如果菜单中的菜单项不跨越整个宽度就居中（目前不支持菜单项宽度基于标题）。默认值：`NO`
     ```objectivec
     centerMenuItems (Bool)
     ```
*   在菜单项目中滚动动画持续时间，以毫秒为单位，默认值：500（即0.5秒）
     ```objectivec
     scrollAnimationDurationOnMenuItemTap (Int)
     ```

## 使用 PageMenu 的 APP

Please let me know if your app in the AppStore uses this library so I can add your app to the list of apps featuring PageMenu.

## Future Work

- [x] Screen rotation support
- [x] Objective-C version
- [ ] Infinite scroll option / Wrap items
- [ ] Carthage support
- [ ] More customization options



## Demo

### 示例一

```objectivec
- (void)viewDidLoad {
    [super viewDidLoad];
    
    UIViewController *viewController1 = [[UIViewController alloc] init];
    viewController1.title = @"蔬菜";
    viewController1.view.backgroundColor = [UIColor robinEggColor];
    
    UIViewController *viewController2 = [[UIViewController alloc] init];
    viewController2.title = @"水果";
    viewController2.view.backgroundColor = [UIColor easterPinkColor];
    
    NSArray *controllerArray = @[viewController1, viewController2];
    
    NSDictionary *parameters = @{
                                
             // 【1.0】颜色
             // 【1.1】Scroller View 的背景色
//             CAPSPageMenuOptionViewBackgroundColor:[UIColor whiteColor],
             // 【1.2】设置滚动菜单背景颜色
             CAPSPageMenuOptionScrollMenuBackgroundColor:[UIColor beigeColor],
             // 【1.3】选择指示器颜色（底部菜单线颜色）
             CAPSPageMenuOptionSelectionIndicatorColor:[UIColor turquoiseColor],
             // 【1.4】所选菜单项标签文本颜色
             CAPSPageMenuOptionSelectedMenuItemLabelColor:[UIColor turquoiseColor],
             // 【1.5】未选择的菜单项标签文本颜色
             CAPSPageMenuOptionUnselectedMenuItemLabelColor:[UIColor lightGrayColor],
             // 【1.6】菜单项分隔符颜色（用于分段控件样式）
             CAPSPageMenuOptionMenuItemSeparatorColor:[UIColor grapefruitColor],
             // 【1.7】底部菜单线颜色
//             CAPSPageMenuOptionBottomMenuHairlineColor:[UIColor coralColor],
             
             // 【2.0】尺寸
             // 【2.1】滚动菜单高度，默认值：34.0
//             CAPSPageMenuOptionMenuHeight: @(40.0),
             // 【2.2】滚动菜单边距,默认值：15.0
//             CAPSPageMenuOptionMenuMargin:@(10),
             // 【2.3】滚动菜单项宽度，默认值：111.0
//             CAPSPageMenuOptionMenuItemWidth:@(150),
             // 【2.4】选择指示器高度，默认值：3.0
//             CAPSPageMenuOptionSelectionIndicatorHeight:@(5),
             
             // 【3.0】Segmented Control
             // 【3.1】将 PageMenu 作为分段控件使用
             CAPSPageMenuOptionUseMenuLikeSegmentedControl:@(YES),
             // 【3.2】菜单项分隔符宽度（以像素为单位）
//             CAPSPageMenuOptionMenuItemSeparatorWidth:@(5.0),
             // 【3.3】菜单项分隔符高度，以菜单高度的百分比表示
//             CAPSPageMenuOptionMenuItemSeparatorPercentageHeight:@(0.5),
             // 【3.4】菜单项分隔符具有圆形边
//             CAPSPageMenuOptionMenuItemSeparatorRoundEdges:@(YES),
//
             
             // 【4.0】其他
             // 【4.1】菜单项标题文本字体
//             CAPSPageMenuOptionMenuItemFont:[UIFont systemFontOfSize:17.0f],
             // 【4.2】添加菜单底部分割线
             CAPSPageMenuOptionAddBottomMenuHairline:@(NO),
//             // 【4.3】菜单项宽度基于标题文字宽度
//             CAPSPageMenuOptionMenuItemWidthBasedOnTitleTextWidth:@(YES)
             // 【4.4】禁用/启用滚动视图控制器的水平反弹
             CAPSPageMenuOptionEnableHorizontalBounce:@(NO),
//             // 【4.5】隐藏/取消隐藏顶部菜单栏
//             CAPSPageMenuOptionHideTopMenuBar:@(YES)
             // 【4.6】菜单中的菜单项如果不跨越整个宽度就居中
//             CAPSPageMenuOptionCenterMenuItems:@(YES)
             
                                 };
    
    CGRect pageMenuRect = CGRectMake(0.0,
                                     0.0,
                                     self.view.frame.size.width,
                                     self.view.frame.size.height);
    _pageMenu = [[CAPSPageMenu alloc] initWithViewControllers:controllerArray
                                                        frame:pageMenuRect
                                                      options:parameters];
    
    [self.view addSubview:_pageMenu.view];
    
}

```

运行效果：

![](http://upload-images.jianshu.io/upload_images/2648731-98c2c1d544937aeb.gif?imageMogr2/auto-orient/strip)


### 示例二

```objectivec
- (void)viewDidLoad {
    [super viewDidLoad];
    [self.PageContainerView addSubview:self.pageMenu.view];
}

#pragma mark - Custom Accessors 

// 缴费明细列表
- (HQLInsurancePaymentListViewController *)leftListViewController {
    if (!_leftListViewController) {
        _leftListViewController = [[HQLInsurancePaymentListViewController alloc] initWithNibName:NSStringFromClass([HQLInsurancePaymentListViewController class]) bundle:nil];
        _leftListViewController.title = @"缴费明细";
        _leftListViewController.delegate = self;
    }
    return _leftListViewController;
}

// 支出明细列表
- (HQLInsurancePaymentListViewController *)rightListViewController {
    if (!_rightListViewController) {
        _rightListViewController = [[HQLInsurancePaymentListViewController alloc] initWithNibName:NSStringFromClass([HQLInsurancePaymentListViewController class]) bundle:nil];
        _rightListViewController.title = @"支出明细";
    }
    return _rightListViewController;
}

// 分页菜单控制器
- (CAPSPageMenu *)pageMenu {
    if (!_pageMenu) {
        NSArray *controllerArray = @[ self.leftListViewController,
                                      self.rightListViewController ];
        
        NSDictionary *parameters = @{
                                     
             // 【1.2】设置滚动菜单背景颜色
             CAPSPageMenuOptionScrollMenuBackgroundColor:[UIColor whiteColor],
             // 【1.3】选择指示器颜色（底部菜单线颜色）
             CAPSPageMenuOptionSelectionIndicatorColor:HQLThemeColor,
             // 【1.4】所选菜单项标签文本颜色
             CAPSPageMenuOptionSelectedMenuItemLabelColor:HQLThemeColor,
    
             // 【2.0】尺寸
             // 【2.1】滚动菜单高度，默认值：34.0
             CAPSPageMenuOptionMenuHeight: @(40.0),
             // 【2.2】滚动菜单边距,默认值：15.0
//             CAPSPageMenuOptionMenuMargin:@(10),
             // 【2.3】滚动菜单项宽度，默认值：111.0
//             CAPSPageMenuOptionMenuItemWidth:@(150),
             // 【2.4】选择指示器高度，默认值：3.0
//             CAPSPageMenuOptionSelectionIndicatorHeight:@(5),
             
             // 【3.0】Segmented Control
             // 【3.1】将 PageMenu 作为分段控件使用
             CAPSPageMenuOptionUseMenuLikeSegmentedControl:@(YES),

             
             // 【4.0】其他
             // 【4.1】菜单项标题文本字体
//             CAPSPageMenuOptionMenuItemFont:[UIFont systemFontOfSize:17.0f],
             // 【4.2】添加菜单底部分割线
             CAPSPageMenuOptionAddBottomMenuHairline:@(NO),
             //             // 【4.3】菜单项宽度基于标题文字宽度
//             CAPSPageMenuOptionMenuItemWidthBasedOnTitleTextWidth:@(YES)
             // 【4.4】禁用/启用滚动视图控制器的水平反弹
             CAPSPageMenuOptionEnableHorizontalBounce:@(NO),
                                     };
        
        CGRect pageMenuRect = self.PageContainerView.bounds;
        _pageMenu = [[CAPSPageMenu alloc] initWithViewControllers:controllerArray
                                                            frame:pageMenuRect
                                                          options:parameters];
    }
    return _pageMenu;
}
```
参照运行效果：

![](http://upload-images.jianshu.io/upload_images/2648731-96229d8d06aaf87f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 其他框架

- [分页切换控制器](http://www.jianshu.com/p/e0cfc2a09b1b):[XBScrollPageController](https://github.com/changjianfeishui/XBScrollPageController) star:100+
- [WMPageController](https://github.com/wangmchn/WMPageController/blob/master/README_zh-CN.md) star：1300+
- [MXSegmentedPager](https://github.com/maxep/MXSegmentedPager) star:700+
- [XHTwitterPaggingViewer](https://github.com/xhzengAIB/XHTwitterPaggingViewer) star:200+
