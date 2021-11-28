> 学习使用 `tintColor` 属性 

### 参考

- [GitHub 源码：shinobicontrols/iOS7-day-by-day](https://github.com/ShinobiControls/iOS7-day-by-day)

* [天天品尝iOS7甜点 :: Day 6 :: Tint Color](http://blog.kingiol.com/2014/01/09/ios7-day-by-day-day6-tint-color/)




### tintColor 属性

```objective-c
@property(null_resettable, nonatomic, strong) UIColor *tintColor NS_AVAILABLE_IOS(7_0);
```

* `tintColor` 属性是 iOS 7.0 新增属性，被使用在`UIView`中以改变应用程序的外观（我的理解是，类似于给所有 UI 控件统一设置主题色效果）；
* 该属性是 `UIColor` 类型，默认为系统颜色（蓝色）；
* 它将会运用父视图层次的颜色来进行着色。可以通过设置 root view controller 的 `tintColor` 来改变系统整体的颜色。



### 特点：**继承**、**重写**、**传播**。

> * **继承**：只要一个 **UIView** 的 subview 没有明确指定 `tintColor`，那么这个 **UIView** 的 `tintColor ` 就会被它的 subview 所继承！在一个 App 中，最顶层的 view 就是 window，因此，只要修改 window 的 `tintColor`，那么所有 view 的 `tintColor` 就都会跟着改变。(这种说法其实并不严谨，请耐心继续看下去^_^)。
>
> * **重写**：如果明确指定了某个 view 的 `tintColor`， 那么这个 view 就不会继承其 superview 的 `tintColor`。而且自此, 这个 view 的 subview 的 `tintColor` 会发生改变。
>
> * 传播：一个 view 的 `tintColor` 的改变会立即向下传播, 影响其所有的 subview，直至它的一个 subview 明确指定了 `tintColor` 为止。
>
>   ——[UIView并没想的那么简单 - tintColor揭秘](http://www.jianshu.com/p/4bcaf6349133)



### Tint color of existing iOS controller - 使用tint color为iOS中已经存在的控件进行着色

示例代码：

```objective-c
- (IBAction)changeColorHandle:(id)sender {
    // 生成随机色
    CGFloat hue = ( arc4random() % 256 / 256.0 );
    CGFloat saturation = ( arc4random() % 128 / 256.0 ) + 0.5;
    CGFloat brightness = ( arc4random() % 128 / 256.0 ) + 0.5;
    UIColor *color = [UIColor colorWithHue:hue saturation:saturation brightness:brightness alpha:1];
    // 设置 tintColor
    self.view.tintColor = color;
}
```

效果：只要修改根视图控制器的 `tintColor` 属性，该页面下的所有 UI 控件颜色都会随之改变。

![](http://upload-images.jianshu.io/upload_images/2648731-ae94855287241127.gif?imageMogr2/auto-orient/strip)



### 关于 UIProgressView

参考的文章称 `tintColor` 属性并不会影响 UIProgressView，因为其有两个描述 `tintColor` 的属性：

```objective-c
// 填充进度条的颜色
@property(nonatomic, strong, nullable) UIColor* progressTintColor;
// 未填充进度条的颜色
@property(nonatomic, strong, nullable) UIColor* trackTintColor;
```

需要进行额外设置：

```objective-c
- (void)updateProgressViewTint {
    self.progressView.progressTintColor = self.view.tintColor;
}
```

#### ✏️ 更正

注释以上代码后测试，结果并不是这样，UIProgressView 的 `progressTintColor` 值是会跟随 `tintColor` 的变化而变化的。



### Tint Dimming - 颜色变暗

#### UIViewTintAdjustmentMode

```objective-c
typedef NS_ENUM(NSInteger, UIViewTintAdjustmentMode) {
    UIViewTintAdjustmentModeAutomatic,
    
    UIViewTintAdjustmentModeNormal,
    UIViewTintAdjustmentModeDimmed,
} NS_ENUM_AVAILABLE_IOS(7_0);
```

示例代码：

```objective-c
- (IBAction)dimTintHandle:(id)sender {
    if (self.dimTintSwitch.isOn) {
        self.view.tintAdjustmentMode = UIViewTintAdjustmentModeDimmed;
    }else {
        self.view.tintAdjustmentMode = UIViewTintAdjustmentModeNormal;
    }
}
```

效果：设置`UIViewTintAdjustmentModeDimmed` 属性以后，所有 UI 控件的颜色会变暗，适合沉浸式体验的场景。

![](http://upload-images.jianshu.io/upload_images/2648731-a1d0b4b0ee48936f.gif?imageMogr2/auto-orient/strip)



### Using tint color in custom views - 给自定义视图进行着色

自定义 UIView 子类需要覆盖以下方法：

```objective-c
/*
 The -tintColorDidChange message is sent to appropriate subviews of a view when its tintColor is changed by client code or to subviews in the view hierarchy of a view whose tintColor is implicitly changed when its superview or tintAdjustmentMode changes.
 */
- (void)tintColorDidChange NS_AVAILABLE_IOS(7_0);
```

创建一个自定义视图类：

```objective-c
//  ***********************************
//  HQLCustomView.h
#import <UIKit/UIKit.h>
@interface HQLCustomView : UIView
@end
  
//  ***********************************
//  HQLCustomView.m
#import "HQLCustomView.h"

@implementation HQLCustomView {
    UIView *_tintColorBlock;
    UILabel *_greyLabel;
    UILabel *_tintColorLabel;
}

- (id)initWithCoder:(NSCoder *)aDecoder {
    self = [super initWithCoder:aDecoder];
    if (!self) {
        return nil;
    }
    self.backgroundColor = [UIColor clearColor];
    [self prepareSubviews];
    return self;
}

- (void)prepareSubviews {
    _tintColorBlock = [[UIView alloc] init];
    _tintColorBlock.backgroundColor = self.tintColor;
    [self addSubview:_tintColorBlock];
    
    _greyLabel = [UILabel new];
    _greyLabel.text = @"Grey Label";
    _greyLabel.textColor = [UIColor grayColor];
    [_greyLabel sizeToFit];
    [self addSubview:_greyLabel];
    
    _tintColorLabel = [UILabel new];
    _tintColorLabel.text = @"Tint Color Label";
    _tintColorLabel.textColor = self.tintColor;
    [_tintColorLabel sizeToFit];
    [self addSubview:_tintColorLabel];
}

- (void)layoutSubviews {
    _tintColorBlock.frame = CGRectMake(0, 0, CGRectGetWidth(self.bounds) / 3, CGRectGetHeight(self.bounds));
    
    CGRect frame = _greyLabel.frame;
    frame.origin.x = CGRectGetWidth(self.bounds) / 3 + 10;
    frame.origin.y = 0;
    _greyLabel.frame = frame;
    
    frame = _tintColorLabel.frame;
    frame.origin.x = CGRectGetWidth(self.bounds) / 3 + 10;
    frame.origin.y = CGRectGetHeight(self.bounds) / 2;
    _tintColorLabel.frame = frame;
}

// 当tintColor或者tintAdjustmentMode属性发生变化时调用
- (void)tintColorDidChange {
    _tintColorBlock.backgroundColor = self.tintColor;
    _tintColorLabel.textColor = self.tintColor;
}

@end
```

![](http://upload-images.jianshu.io/upload_images/2648731-6174f42818ec8b61.gif?imageMogr2/auto-orient/strip)



### Tinting images with tintColor - 给图像着色

```objective-c
// 加载图片
UIImage *gitHubImg = [UIImage imageNamed:@"gitHub"];
// 设置图片渲染模式
gitHubImg = [gitHubImg imageWithRenderingMode:UIImageRenderingModeAlwaysTemplate];
// 添加到视图
self.tintedImageView.image = gitHubImg;
self.tintedImageView.contentMode = UIViewContentModeScaleAspectFit;
```

原图：

![](http://upload-images.jianshu.io/upload_images/2648731-6d989755b29f8caa.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



实际效果：

![](http://upload-images.jianshu.io/upload_images/2648731-fc6008938b1e272f.gif?imageMogr2/auto-orient/strip)
