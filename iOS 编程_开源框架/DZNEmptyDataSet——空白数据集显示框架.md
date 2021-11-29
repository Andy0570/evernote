* GitHub: [DZNEmptyDataSet](https://github.com/dzenbot/DZNEmptyDataSet)
* star: 11k

注：以下内容来源于官方源码、 README 文档、Demo 应用以及个人使用总结 ！


# DZNEmptyDataSet

[DZNEmptyDataSet](https://github.com/dzenbot/DZNEmptyDataSet) 是基于 `UITableView/UICollectionView` 的扩展（category）类，用于在空白页面显示提示信息。

这是 iOS 内建的标准，用于处理空白列表和空白集合视图。默认情况下，如果你的列表视图是空白的，屏幕上什么也不会显示，它给用户的体验不是很好。

集成 [DZNEmptyDataSet](https://github.com/dzenbot/DZNEmptyDataSet) 之后，你只需要实现相关协议，就可以很好地处理空白视图，合理美观地显示出提示信息。

![](http://upload-images.jianshu.io/upload_images/2648731-bea652c96dcea06f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](http://upload-images.jianshu.io/upload_images/2648731-fe7683768640c517.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 其他效果图参考

* [**pttrns** - Empty States](https://pttrns.com/?did=1&scid=30)
* [**calltoidea** - Empty Page](http://www.calltoidea.com/empty/)
* [**Mobile Patterns** - Empty Data Sets](http://www.mobile-patterns.com/empty-data-sets)

## 使用该框架的项目

[将你的项目添加到列表中](https://github.com/dzenbot/DZNEmptyDataSet/wiki/Projects-using-DZNEmptyDataSet) 并且提供一张（320px）的效果图。


## 空数据设计模式（The Empty Data Set Pattern）

空数据设计模式（The Empty Data Set Pattern）也被称为 [Empty state](http://emptystat.es) 或者 [Blank Slate](http://patternry.com/p=blank-slate/)。

大多数应用程序会显示列表视图（`UITableView`）、集合视图（`UICollectionView`），但有些时候这些页面可能是空白的，特别是对于那些刚创建空账户的新用户来说。 空白界面会对用户造成不知道如何进行下一步操作的困惑，因为用户不知道屏幕空白的原因是错误、Bug、网络异常，还是用户应该自己新建内容以恢复APP的正常状态。

请阅读这篇非常有趣的文章 [Designing For The Empty States](https://tympanus.net/codrops/2013/01/09/designing-for-the-empty-states/)。

在 [iOS 9人机界面指南 1.4.2](https://isux.tencent.com/ios9-guideline-ch1.html) 中也提及过类似的指引：

> **如果应用中所有的功能当前都不可用，那么应该显示一些内容来解释当前的情形，并建议用户如何进行后续操作。**这部分内容给予了用户以反馈，使用户相信你的应用现在没问题。同时这也可以稳定用户情绪，让他们决定是否要采取纠正措施，继续使用应用，还是切换到另一个应用。
>
> ![](http://upload-images.jianshu.io/upload_images/2648731-1ff31e641b5e8248.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/320)



[Empty Data Sets](https://pttrns.com/?did=1&scid=30) 有助于：

* 避免使用空白屏幕，并向用户传达屏幕空白的原因。
* 用户指引（特别是作为引导页面）。
* 避免其他中断机制，如显示错误警报。
* 一致性和改善用户体验。
* 传递品牌价值。



## 特性

* 兼容 **UITableView** 和 **UICollectionView** 。 也兼容 **UISearchDisplayController** 和 **UIScrollView** 。
* 通过显示图片、标题、详细文本、按钮，提供布局外观的多种可能性。
* 使用 **NSAttributedString** 类提供更容易定制的外观。
* 使用 **Auto Layout** 以自动将内容集中到表格视图，并支持自动旋转。 也接受自定义垂直和水平对齐。
* 自定义背景颜色。
* 允许在整个表格矩形上轻敲手势（有助于放弃第一个响应者或类似操作）。
* 提供更高级的定制，允许自定义视图。
* 兼容 **Storyboard**。
* 兼容iOS 6，tvOS 9或更高版本。
* 兼容iPhone，iPad和Apple TV。
* 支持 App Store 。

这个库已经被设计为不需要通过扩展（extend） **UITableView** 或 **UICollectionView** 类的方式来实现了。 使用 **UITableViewController** 或 **UICollectionViewController** 类仍然可以奏效。 只要通过遵循 `DZNEmptyDataSetSource` 和 `DZNEmptyDataSetDelegate` 协议，您将能够完全自定义应用程序的空状态的内容和外观。



## 安装

支持 [CocoaPods](http://cocoapods.org/?q=DZNEmptyDataSet) 导入

```ruby
pod 'DZNEmptyDataSet'
```

## 使用

完整文档参考：[CocoaPods 自动生成文档](http://cocoadocs.org/docsets/DZNEmptyDataSet/1.8.1/)

### 导入


手动导入：
```objectivec
#import "UIScrollView+EmptyDataSet.h"
```

作为框架导入：
```objectivec
#import <DZNEmptyDataSet/UIScrollView+EmptyDataSet.h>
```

### 遵循协议

在需要显示空白数据集的视图控制器中，声明该视图控制器遵守`DZNEmptyDataSetSource`、`DZNEmptyDataSetDelegate` 协议：

```objectivec
// 遵守 DZNEmptyDataSetSource 、DZNEmptyDataSetDelegate 协议
@interface MainViewController : UITableViewController <DZNEmptyDataSetSource, DZNEmptyDataSetDelegate>

- (void)viewDidLoad {
    [super viewDidLoad];

    self.tableView.emptyDataSetSource = self;
    self.tableView.emptyDataSetDelegate = self;
}
```

### 实现数据源协议 DZNEmptyDataSetSource

实现该协议可以设置你想要在空白页面显示的内容，并且充分利用 `NSAttributedString` 类来自定义文本样式。

#### 空白页显示图片

```objectivec
// MARK: 空白页显示图片
- (UIImage *)imageForEmptyDataSet:(UIScrollView *)scrollView {
    return [UIImage imageNamed:@"lion"];
}
```

实现 `<DZNEmptyDataSetSource>` 协议的 `imageForEmptyDataSet:` 方法，返回一张图片即可。

页面显示效果如下图所示：

![](https://upload-images.jianshu.io/upload_images/2648731-476acb92e7ce5e2d.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 空白页显示图片和标题

继续上一个示例，在图片下方添加标题文字：

```objectivec
// MARK: 空白页显示图片
- (UIImage *)imageForEmptyDataSet:(UIScrollView *)scrollView {
    return [UIImage imageNamed:@"lion"];
}

// MARK: 空白页显示标题
- (NSAttributedString *)titleForEmptyDataSet:(UIScrollView *)scrollView {
    NSString *title = @"狮子王";
    NSDictionary *attributes = @{
        NSFontAttributeName:[UIFont boldSystemFontOfSize:18.0f],
        NSForegroundColorAttributeName:[UIColor darkGrayColor]
    };
    return [[NSAttributedString alloc] initWithString:title attributes:attributes];
}
```

页面显示效果如下图所示：

![](https://upload-images.jianshu.io/upload_images/2648731-f6f84db5de6b1401.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 空白页显示图片、标题、详细描述

这一次，除了在空白页面显示一张图片和标题，我们还可以在下方添加更详细的描述文字：

```objectivec
// MARK: 空白页显示图片
- (UIImage *)imageForEmptyDataSet:(UIScrollView *)scrollView {
    return [UIImage imageNamed:@"lion"];
}

// MARK: 空白页显示标题
- (NSAttributedString *)titleForEmptyDataSet:(UIScrollView *)scrollView {
    NSString *title = @"狮子王";
    NSDictionary *attributes = @{
        NSFontAttributeName:[UIFont boldSystemFontOfSize:18.0f],
        NSForegroundColorAttributeName:[UIColor darkGrayColor]
    };
    return [[NSAttributedString alloc] initWithString:title attributes:attributes];
}

// MARK: 空白页显示详细描述
- (NSAttributedString *)descriptionForEmptyDataSet:(UIScrollView *)scrollView {
    NSString *text = @"我只在必要时才勇敢，勇敢并不意味着要到处闯祸！";
  
    NSMutableParagraphStyle *paragraph = [NSMutableParagraphStyle new];
    paragraph.lineBreakMode = NSLineBreakByWordWrapping;
    paragraph.alignment = NSTextAlignmentCenter;
    
    NSDictionary *attributes = @{
        NSFontAttributeName:[UIFont systemFontOfSize:14.0f],
        NSForegroundColorAttributeName:[UIColor lightGrayColor],
        NSParagraphStyleAttributeName:paragraph
    };
    
    return [[NSAttributedString alloc] initWithString:text attributes:attributes];
}
```

页面显示效果如下图所示：

![](https://upload-images.jianshu.io/upload_images/2648731-d6fd09fb2e6381b8.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 设置内容的垂直偏移量

你还可以调整内容视图的垂直对齐（垂直偏移量）方式：

```objectivec
// 向上偏移量为表头视图高度/2
- (CGFloat)verticalOffsetForEmptyDataSet:(UIScrollView *)scrollView {
    return -self.tableView.tableHeaderView.frame.size.height/2.0f;
}
```

或者返回一个固定值：
```
- (CGFloat)verticalOffsetForEmptyDataSet:(UIScrollView *)scrollView {
    return -64;
}
```

页面效果如下所示：

![](https://upload-images.jianshu.io/upload_images/2648731-8f8caff44054c1b3.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 设置内容元素之间的垂直间距

你也可以**设置所有组件彼此之间的上下间距**（默认间距为11 pt）：

```objectivec
- (CGFloat)spaceHeightForEmptyDataSet:(UIScrollView *)scrollView {
    return 50.0f;
}
```

纯粹为了测试，我把这个垂直间距的值设置的很大，页面效果如下所示：

![](https://upload-images.jianshu.io/upload_images/2648731-868f86687d190703.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 空白页添加按钮

除了在空白页添加静态的图片和文本，我们还可以在空白页添加可交互的按钮。设置空白页按钮的文字：

```objectivec
#pragma mark - <DZNEmptyDataSetSource>

// MARK: 空白页显示图片
- (UIImage *)imageForEmptyDataSet:(UIScrollView *)scrollView {
    return [UIImage imageNamed:@"unlogin"];
}

// MARK: 空白页显示标题
- (NSAttributedString *)titleForEmptyDataSet:(UIScrollView *)scrollView {
    NSString *title = @"您当前未登录";
    NSDictionary *attributes = @{
        NSFontAttributeName:[UIFont boldSystemFontOfSize:14.0f],
        NSForegroundColorAttributeName:[UIColor lightGrayColor]
    };
    return [[NSAttributedString alloc] initWithString:title attributes:attributes];
}

// MARK: 空白页添加按钮，设置按钮文字
- (NSAttributedString *)buttonTitleForEmptyDataSet:(UIScrollView *)scrollView forState:(UIControlState)state {
    // 设置按钮标题
    NSString *buttonTitle = @"点击登录";
    NSDictionary *attributes = @{
        NSFontAttributeName:[UIFont boldSystemFontOfSize:17.0f],
        NSForegroundColorAttributeName: [UIColor flatSkyBlueColor]
    };
    return [[NSAttributedString alloc] initWithString:buttonTitle attributes:attributes];
}

#pragma mark - <DZNEmptyDataSetDelegate>

- (void)emptyDataSet:(UIScrollView *)scrollView didTapButton:(UIButton *)button {
    // 处理空白页面按钮点击事件
    NSLog(@"处理空白页面按钮点击事件");
    
}
```

正如以上示例代码所示，
实现 `<DZNEmptyDataSetSource>` 协议的  `buttonTitleForEmptyDataSet:forState:` 方法，并返回一个 `NSAttributedString` 实例来设置按钮标题。
实现 `<DZNEmptyDataSetDelegate>` 协议的 `emptyDataSet:didTapButton:` 方法，可以处理该按钮的点击响应事件。

页面效果如下所示：

![](https://upload-images.jianshu.io/upload_images/2648731-97ce269091f3b904.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

注意到，设置按钮标题的 `buttonTitleForEmptyDataSet:forState:` 方法中，还返回了 `UIControlState` 属性，因此我们还可以**设置按钮高亮或者默认状态下的颜色**，示例代码如下：

```objectivec
- (NSAttributedString *)buttonTitleForEmptyDataSet:(UIScrollView *)scrollView forState:(UIControlState)state {
    // 设置按钮标题
    NSString *buttonTitle = @"按钮标题";
    UIColor *buttonColor = [UIColor colorWithHexString:(state == UIControlStateNormal) ? @"007ee5" : @"48a1ea"];
    NSDictionary *attributes = @{
        NSFontAttributeName:[UIFont systemFontOfSize:15.0],
        NSForegroundColorAttributeName: buttonColor
    };
    return [[NSAttributedString alloc] initWithString:buttonTitle attributes:attributes];
}
```
说明：
* `colorWithHexString:` 表示通过十六进制值设置颜色，该方法来自 [Chameleon](https://github.com/viccalexander/Chameleon) 颜色框架。
* `statement ? True : False` 是一个三元表达式，判断值为真，则执行 True，否则执行 False。


#### 空白页添加按钮，个性化按钮标题

空白页面中仅添加一张图片和一个按钮标题，把按钮标题中 「点击重试」四个字颜色高亮加粗：

```objectivec
#pragma mark - <DZNEmptyDataSetSource>

// MARK: 空白页显示图片
- (UIImage *)imageForEmptyDataSet:(UIScrollView *)scrollView {
    return [UIImage imageNamed:@"placeholder_no_network"];
}

// MARK: 空白页添加按钮，设置按钮文字
- (NSAttributedString *)buttonTitleForEmptyDataSet:(UIScrollView *)scrollView forState:(UIControlState)state {
    // 设置按钮标题
    NSString *buttonTitle = @"网络不给力，请点击重试哦~";

    NSMutableAttributedString *attributedString = [[NSMutableAttributedString alloc] initWithString:buttonTitle];
    // 设置所有字体大小为 #15
    [attributedString addAttribute:NSFontAttributeName
                             value:[UIFont systemFontOfSize:15.0]
                             range:NSMakeRange(0, buttonTitle.length)];
    // 设置所有字体颜色为浅灰色
    [attributedString addAttribute:NSForegroundColorAttributeName
                             value:[UIColor lightGrayColor]
                             range:NSMakeRange(0, buttonTitle.length)];
    // 设置指定4个字体为蓝色
    [attributedString addAttribute:NSForegroundColorAttributeName
                             value:HexColor(@"#007EE5")
                             range:NSMakeRange(7, 4)];
    return attributedString;
    
}

// MARK: 空白页背景颜色
- (nullable UIColor *)backgroundColorForEmptyDataSet:(UIScrollView *)scrollView {
    return [UIColor colorWithHexString:@"#f0f3f5"];
}

// MARK: 设置空白页内容的垂直便宜量
- (CGFloat)verticalOffsetForEmptyDataSet:(UIScrollView *)scrollView {
    return -70.0f;
}

#pragma mark - <DZNEmptyDataSetDelegate>

- (void)emptyDataSet:(UIScrollView *)scrollView didTapButton:(UIButton *)button {
    // 处理空白页面按钮点击事件
    NSLog(@"处理空白页面按钮点击事件");
}

- (void)emptyDataSetWillAppear:(UIScrollView *)scrollView {
    self.tableView.contentOffset = CGPointZero;
}
```

页面效果如下所示：

![](https://upload-images.jianshu.io/upload_images/2648731-0eab292f98cea24d.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



#### 空白页添加按钮，设置按钮背景图片

通过实现 `<DZNEmptyDataSetSource>` 协议的 `buttonBackgroundImageForEmptyDataSet: forState:` 方法，你可以设置默认状态（即 `UIControlStateNormal`）和高亮状态（即 `UIControlStateHighlighted`）下的按钮背景图片。

背景图片类似这样的：

![](https://upload-images.jianshu.io/upload_images/2648731-1fd35ab4463d0695.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

示例代码如下：

```objectivec
#pragma mark - <DZNEmptyDataSetSource>

// MARK: 空白页显示图片
- (UIImage *)imageForEmptyDataSet:(UIScrollView *)scrollView {
    if (self.isLoading) {
        return [UIImage imageNamed:@"loading_imgBlue_78x78"];
    } else {
        return [UIImage imageNamed:@"placeholder_foursquare"];
    }
}

// MARK: 空白页图片动画
- (CAAnimation *)imageAnimationForEmptyDataSet:(UIScrollView *)scrollView {
    CABasicAnimation *animation = [CABasicAnimation animationWithKeyPath:@"transform"];
    animation.fromValue = [NSValue valueWithCATransform3D:CATransform3DIdentity];
    animation.toValue = [NSValue valueWithCATransform3D: CATransform3DMakeRotation(M_PI_2, 0.0, 0.0, 1.0) ];
    animation.duration = 0.25;
    animation.cumulative = YES;
    animation.repeatCount = MAXFLOAT;
    
    return animation;
}

// MARK: 空白页显示详细描述
- (NSAttributedString *)descriptionForEmptyDataSet:(UIScrollView *)scrollView {
    NSString *text = @"Nobody has liked or commented on your check-ins yet.";
  
    NSMutableParagraphStyle *paragraph = [NSMutableParagraphStyle new];
    paragraph.lineBreakMode = NSLineBreakByWordWrapping;
    paragraph.alignment = NSTextAlignmentCenter;
    
    NSDictionary *attributes = @{
        NSFontAttributeName:[UIFont systemFontOfSize:14.0f],
        NSForegroundColorAttributeName:[UIColor colorWithHexString:@"#cecbc6"],
        NSParagraphStyleAttributeName:paragraph
    };
    
    return [[NSAttributedString alloc] initWithString:text attributes:attributes];
}

// MARK: 空白页添加按钮，设置按钮文字
- (NSAttributedString *)buttonTitleForEmptyDataSet:(UIScrollView *)scrollView forState:(UIControlState)state {
    // 设置按钮标题
    NSString *buttonTitle = @"Add friends to get started!";
    UIColor *buttonColor = [UIColor colorWithHexString:(state == UIControlStateNormal) ? @"00aeef" : @"ffffff"];
    NSDictionary *attributes = @{
        NSFontAttributeName:[UIFont systemFontOfSize:14.0],
        NSForegroundColorAttributeName: buttonColor
    };
    return [[NSAttributedString alloc] initWithString:buttonTitle attributes:attributes];
}

// MARK: 空白页添加按钮，设置按钮背景图片
- (UIImage *)buttonBackgroundImageForEmptyDataSet:(UIScrollView *)scrollView forState:(UIControlState)state {
   
    UIImage *image;
    if (state == UIControlStateNormal) {
        image = [UIImage imageNamed:@"button_background_foursquare_normal"];
    }
    if (state == UIControlStateHighlighted) {
        image = [UIImage imageNamed:@"button_background_foursquare_highlight"];
    }
    
    UIEdgeInsets capInsets = UIEdgeInsetsMake(25.0, 25.0, 25.0, 25.0);
    UIEdgeInsets rectInsets = UIEdgeInsetsMake(0.0, 10, 0.0, 10);

    return [[image resizableImageWithCapInsets:capInsets resizingMode:UIImageResizingModeStretch] imageWithAlignmentRectInsets:rectInsets];
}

// MARK: 空白页背景颜色
- (nullable UIColor *)backgroundColorForEmptyDataSet:(UIScrollView *)scrollView {
    return [UIColor colorWithHexString:@"#fcfcfa"];
}

// MARK: 设置各个视图元素之间的垂直间距，默认为 11pt
- (CGFloat)spaceHeightForEmptyDataSet:(UIScrollView *)scrollView {
    return 9.0;
}

#pragma mark - <DZNEmptyDataSetDelegate>

- (void)emptyDataSet:(UIScrollView *)scrollView didTapButton:(UIButton *)button {
    // 处理空白页面按钮点击事件
    NSLog(@"处理空白页面按钮点击事件");
    
    self.loading = YES;
    
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(3 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        self.loading = NO;
    });
    
}

- (BOOL)emptyDataSetShouldAnimateImageView:(UIScrollView *)scrollView {
    return self.isLoading;
}
```

以上代码设置了按钮文字标题，和按钮的背景图片。页面效果如下所示：

![](https://upload-images.jianshu.io/upload_images/2648731-8d7f117dea5be930.gif?imageMogr2/auto-orient/strip)




### 设置按钮的背景颜色

通过设置按钮背景图片的方法也可以设置按钮背景颜色：
```objectivec
- (UIImage *)buttonBackgroundImageForEmptyDataSet:(UIScrollView *)scrollView forState:(UIControlState)state {
    
    UIImage *image = [UIImage imageWithColor:[UIColor greenColor]];
    return image;
}
```
以上代码使用了 YYKit 框架中的 `imageWithColor:` 方法，可以通过颜色生成纯色图片。


### 设置按钮图片

如果你的按钮定制化需求很高，也通过实现 `buttonImageForEmptyDataSet: forState:` 方法，你可以直接把按钮设置成一张图片：

```objectivec
#pragma mark - <DZNEmptyDataSetSource>

// MARK: 空白页显示图片
- (UIImage *)imageForEmptyDataSet:(UIScrollView *)scrollView {
    return [UIImage imageNamed:@"placeholder_network_error_404"];
}

// MARK: 空白页显示标题
- (NSAttributedString *)titleForEmptyDataSet:(UIScrollView *)scrollView {
    NSString *title = @"您当前未连接网络";
    NSDictionary *attributes = @{
        NSFontAttributeName:[UIFont systemFontOfSize:15.0],
        NSForegroundColorAttributeName:[UIColor colorWithHexString:@"#25282b"]
    };
    return [[NSAttributedString alloc] initWithString:title attributes:attributes];
}

// MARK: 空白页添加按钮，设置按钮图片
- (UIImage *)buttonImageForEmptyDataSet:(UIScrollView *)scrollView forState:(UIControlState)state {
    return [UIImage imageNamed:@"button_Image"];
}


#pragma mark - <DZNEmptyDataSetDelegate>

- (void)emptyDataSet:(UIScrollView *)scrollView didTapButton:(UIButton *)button {
    // 处理空白页面按钮点击事件
    NSLog(@"处理空白页面按钮点击事件");
    
}
```

页面效果如下所示：

![](https://upload-images.jianshu.io/upload_images/2648731-c94e1f2278fd80f2.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 设置图片的  `tintColor` 属性

就是设置图片颜色，脑补中。。。


```objectivec
- (UIColor *)imageTintColorForEmptyDataSet:(UIScrollView *)scrollView {
    return [UIColor yellowColor];
}
```

### 设置空白页面的背景色

通过实现 `backgroundColorForEmptyDataSet:` 方法并返回 `UIColor` 实例就可以设置空白页面的背景色了，这个方法，之前的示例代码中也出现过。

我们在上一个示例代码中添加以下代码来对比看看：
```objectivec
// MARK: 空白页背景颜色
- (nullable UIColor *)backgroundColorForEmptyDataSet:(UIScrollView *)scrollView {
    return [UIColor colorWithHexString:@"#f0f3f5"];
}
```

页面效果如下所示：

![](https://upload-images.jianshu.io/upload_images/2648731-972bccfabb21b377.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 设置自定义视图

如果你需要设置更复杂的布局，也可以设置并返回自定义视图：

```objectivec
- (UIView *)customViewForEmptyDataSet:(UIScrollView *)scrollView {
    UIActivityIndicatorView *activityView = [[UIActivityIndicatorView alloc] initWithActivityIndicatorStyle:UIActivityIndicatorViewStyleGray];
    [activityView startAnimating];
    return activityView;
}
```

以上代码在空白页面中显示一个加载视图，页面效果如下所示：

![](http://upload-images.jianshu.io/upload_images/2648731-0826f7ec81c4d729.gif?imageMogr2/auto-orient/strip)


### 设置图片动画

为图片添加旋转动画。

```objectivec
#pragma mark - <DZNEmptyDataSetSource>

// MARK: 空白页显示图片
- (UIImage *)imageForEmptyDataSet:(UIScrollView *)scrollView {
    return [UIImage imageNamed:@"lion"];
}

// MARK: 设置图片动画: 旋转
- (CAAnimation *)imageAnimationForEmptyDataSet:(UIScrollView *)scrollView {
    CABasicAnimation *animation = [CABasicAnimation animationWithKeyPath: @"transform"];
    
    animation.fromValue = [NSValue valueWithCATransform3D:CATransform3DIdentity];
    animation.toValue = [NSValue valueWithCATransform3D:CATransform3DMakeRotation(M_PI_2, 0.0, 0.0, 1.0)];
    
    animation.duration = 0.25;
    animation.cumulative = YES;
    animation.repeatCount = MAXFLOAT;
    
    return animation;
}

#pragma mark - DZNEmptyDataSetDelegate

// 向代理请求图像视图动画权限。 默认值为NO。
// 确保从 imageAnimationForEmptyDataSet 返回有效的 CAAnimation 对象：
- (BOOL)emptyDataSetShouldAnimateImageView:(UIScrollView *)scrollView {
    return YES;
}
```

页面效果如下所示：

![](https://upload-images.jianshu.io/upload_images/2648731-2fc3e9eb22b634a0.gif?imageMogr2/auto-orient/strip)


### 设置图片动画，缩放动画

```objectivec
#pragma mark - <DZNEmptyDataSetSource>

// MARK: 空白页显示图片
- (UIImage *)imageForEmptyDataSet:(UIScrollView *)scrollView {
    return [UIImage imageNamed:@"placeholder_heart"];
}

// MARK: 设置图片动画: 缩放动画
- (CAAnimation *)imageAnimationForEmptyDataSet:(UIScrollView *)scrollView
{
    CABasicAnimation *animation = [CABasicAnimation animationWithKeyPath:@"bounds"];
    animation.duration = 1.25;
    animation.cumulative = NO;
    animation.repeatCount = MAXFLOAT;
    animation.toValue = [NSValue valueWithCGRect:CGRectMake(0, 0, 45, 45)];

    return animation;
}

#pragma mark - DZNEmptyDataSetDelegate

// 向代理请求图像视图动画权限。 默认值为NO。
// 确保从 imageAnimationForEmptyDataSet 返回有效的 CAAnimation 对象：
- (BOOL)emptyDataSetShouldAnimateImageView:(UIScrollView *)scrollView {
    return YES;
}
```

页面效果如下所示：

![](https://upload-images.jianshu.io/upload_images/2648731-8ad95d1b7ea7df2d.gif?imageMogr2/auto-orient/strip)



我们发现在官方的 **Applications** Demo 应用中的空白视图中的动画是这样的：

![](http://upload-images.jianshu.io/upload_images/2648731-3af19724a39a9b49.gif?imageMogr2/auto-orient/strip)

空白视图默认情况下显示一张【静态图片】，当用户点击【静态图片】以后，该图片会被替换成【加载转圈】。

通过阅读源码，可以发现它是这样工作的：
1.  首先在遵循协议的视图控制器实现文件中声明了一个 `BOOL` 类型的变量，用来记录空白页面当前的加载状态：

```objectivec
@property (nonatomic, assign, getter=isLoading) BOOL loading;
```

2. 然后为该属性设置 `setter` 方法，重新加载空数据集视图：

```objectivec
// 设置当前页面的加载状态
- (void)setLoading:(BOOL)loading {
    
    // 如果当前页面的加载状态和之前一样，返回
    if (self.isLoading == loading) {
        return;
    }
    
    // 不一样，则刷新空白页面
    _loading = loading;
    [self.tableView reloadEmptyDataSet];
}
```

3. 接下来要实现几个关联协议

```objectivec
#pragma mark - DZNEmptyDataSetSource
#pragma mark 设置空白页图片
- (UIImage *)imageForEmptyDataSet:(UIScrollView *)scrollView {
    if (self.isLoading) {
        // 圆形加载图片
        return [UIImage imageNamed:@"loading_imgBlue_78x78"];
    }else {
        // 默认静态图片
        return [UIImage imageNamed:@"staticImage"];
    }
}

#pragma mark 图片旋转动画
- (CAAnimation *)imageAnimationForEmptyDataSet:(UIScrollView *)scrollView
{
    CABasicAnimation *animation = [CABasicAnimation animationWithKeyPath:@"transform"];
    animation.fromValue = [NSValue valueWithCATransform3D:CATransform3DIdentity];
    animation.toValue = [NSValue valueWithCATransform3D: CATransform3DMakeRotation(M_PI_2, 0.0, 0.0, 1.0) ];
    animation.duration = 0.25;
    animation.cumulative = YES;
    animation.repeatCount = MAXFLOAT;
    
    return animation;
}

#pragma mark - DZNEmptyDataSetDelegate
#pragma mark 是否开启动画
- (BOOL)emptyDataSetShouldAnimateImageView:(UIScrollView *)scrollView {
    return self.isLoading;
}

#pragma mark 空白页面被点击时刷新页面
- (void)emptyDataSet:(UIScrollView *)scrollView didTapView:(UIView *)view {
    // 空白页面被点击时开启动画，reloadEmptyDataSet
    self.loading = YES;
    // 执行加载任务...
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(3.0 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        // 任务加载完成后关闭动画，reloadEmptyDataSet
        self.loading = NO;
    });
}
```

### 实现代理协议 DZNEmptyDataSetDelegate

通过实现 `DZNEmptyDataSetDelegate` 协议，可以设置你期望从空白页面返回的的行为，并接收用户交互事件。

* 设置是否 **渲染和显示空白页面** (默认值为 `YES`)

```objectivec
- (BOOL)emptyDataSetShouldDisplay:(UIScrollView *)scrollView {
    return YES;
}
```

* 设置是否 **以淡入方式显示空白页面** 。 (默认值为 `YES`)

```objectivec
- (BOOL)emptyDataSetShouldFadeIn:(UIScrollView *)scrollView { 
    return YES;
}
```

* **强制显示空数据集**：当项目数量大于0时，请求代理是否仍应显示空数据集。（默认值为***NO***）

```objectivec
- (BOOL)emptyDataSetShouldBeForcedToDisplay:(UIScrollView *)scrollView;
```



* **获取交互权限**:是否接收用户点击事件 (默认值为 `YES`)：

```objectivec
- (BOOL)emptyDataSetShouldAllowTouch:(UIScrollView *)scrollView {
    // 如果正在加载中，则不响应用户交互。
    return !self.isLoading;
}
```



* **获取滚动权限**（默认值为 `NO`）：

```objectivec
- (BOOL)emptyDataSetShouldAllowScroll:(UIScrollView *)scrollView;
```



* **获取图像动画权限**：是否开启图片动画（默认值为 `NO`）：

```objectivec
- (BOOL)emptyDataSetShouldAnimateImageView:(UIScrollView *)scrollView {
    return YES;
}
```


* 空白数据集 **视图被点击** 时触发该方法：

```objectivec
- (void)emptyDataSet:(UIScrollView *)scrollView didTapView:(UIView *)view {
    // 处理视图点击事件...
}
```



* 空白数据集 **按钮被点击时** 触发该方法：

```objectivec
- (void)emptyDataSet:(UIScrollView *)scrollView didTapButton:(UIButton *)button {
    // 处理按钮点击事件...
}
```



* 空白页将要出现

```objectivec
- (void)emptyDataSetWillAppear:(UIScrollView *)scrollView {
  // 如果你的空白占位图与需求向左，发生偏移，可如下设置：
  self.tableView.contentOffset = CGPointZero;
}
```



* 空白页已经出现

```objectivec
- (void)emptyDataSetDidAppear:(UIScrollView *)scrollView;
```



* 空白页将要消失

```objectivec
- (void)emptyDataSetWillDisappear:(UIScrollView *)scrollView;
```



* 空白页已经消失

```objectivec
- (void)emptyDataSetDidDisappear:(UIScrollView *)scrollView;
```



### 刷新布局

如果你需要刷新空白页面布局，只需调用：

```objectivec
[self.tableView reloadData];
```

或者

```objectivec
[self.collectionView reloadData];
```

这取决于你用的是哪一个视图（`UITableView`/`UICollectionView`）。



### 强制布局更新

你还可以调用 `[self.tableView reloadEmptyDataSet]` 以使当前空白页面布局无效，并触发布局更新，绕过 `-reloadData` 方法。 如果你的数据源上有很多逻辑处理，当你不需要或者想避免调用 `-reloadData` 方法时这可能很有用。 当使用 `UIScrollView` 滚动视图时，`[self.scrollView reloadEmptyDataSet]` 是刷新内容的唯一方法。

### 彩蛋

我在 GitHub 上传了相关代码的实现源码 [iOS-Samples/HQLTableViewDemo](https://github.com/Andy0570/iOS-Samples/tree/master/HQLTableViewDemo)，有需要的可以去看看，当然，你也可以直接去下载 [DZNEmptyDataSet](https://github.com/dzenbot/DZNEmptyDataSet) 中的 Demo，相信你能得到启发。

## 参考

* [27 款 iOS 开源库，让你的开发溜到飞起](https://www.oschina.net/translate/27-ios-open-source-libraries-to-skyrocket-your-development?print)
* [iOS开发之 - DZNEmptyDataSet 空白页占位图](http://www.jianshu.com/p/84308457527e)
* [DZNEmptyDataSet的使用](http://shaojunxiao.com/2016/02/28/DZNEmptyDataSet的使用/)
