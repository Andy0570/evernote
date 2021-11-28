## UIBlurEffectStyle 模糊效果枚举样式

```objective-c
typedef NS_ENUM(NSInteger, UIBlurEffectStyle) {
    UIBlurEffectStyleExtraLight,
    UIBlurEffectStyleLight,
    UIBlurEffectStyleDark,
    UIBlurEffectStyleExtraDark __TVOS_AVAILABLE(10_0) __IOS_PROHIBITED __WATCHOS_PROHIBITED,
    UIBlurEffectStyleRegular NS_ENUM_AVAILABLE_IOS(10_0), // Adapts to user interface style
    UIBlurEffectStyleProminent NS_ENUM_AVAILABLE_IOS(10_0), // Adapts to user interface style
} NS_ENUM_AVAILABLE_IOS(8_0);
```




.h 源码

```objective-c
//
//  UIVisualEffectView.h
//  UIKit
//
//  Copyright (c) 2014-2017 Apple Inc. All rights reserved.
//

#import <UIKit/UIView.h>
#import <UIKit/UIBlurEffect.h>
#import <UIKit/UIVibrancyEffect.h>

NS_ASSUME_NONNULL_BEGIN

/* UIVisualEffectView is a class that provides a simple abstraction over complex visual effects. Depending on the desired effect, the results may affect content layered behind the view or content added to the view's contentView. Please see the notes for each UIVisualEffect for more details.

UIVisualEffectView 是一个为复杂视觉效果提供简单的抽象类。根据所需的效果，结果可能会影响到视图的内容图层或添加到视图的 contentView 上的内容。 请参阅每个UIVisualEffect的注释了解更多详情。

 Proper use of this class requires some assistance on your part. Namely:
 正确使用这个类需要的一些帮助。即：

 • Avoid alpha values < 1 - By default, when a view is partially transparent, the system composites that view and all of its subviews in an offscreen render pass to get the correct translucency. However, UIVisualEffects require being composited as part of the content they are logically layered on top of to look correct. If alpha is less than 1 on the UIVisualEffectView or any of its superviews, many effects will look incorrect or won't show up at all. Setting the alpha on views placed inside the contentView is supported.
 
 避免 alpha 值小于1 - 默认情况下，当视图部分透明时，系统会离屏渲染合成该视图及其所有子视图，以获得正确的半透明效果。 然而，UIVisualEffects 需要被合成为内容的一部分，它们在逻辑上被分层以看起来是正确的。 如果UIVisualEffectView 或其任何父类视图上的 alpha 值小于1，则许多效果将看起来不正确或根本不显示。在contentView 内放置的视图上设置 alpha 是支持的。

 • Judicious masking - Masks have similar semantics to non-opaque views with regards to offscreen rendering. Masks applied to the UIVisualEffectView itself are forwarded to all internal views, including the contentView. You are free to apply a mask to just the contentView. The mask you provide to UIVisualEffectView will not be the view that actually performs the mask. UIKit will make copies of the view to apply to each subview. To reflect a size change to the mask, you must apply the change to the original mask view and reset it on the effect view. Applying a mask to a superview of a UIVisualEffectView (via setMaskView: or the layer's mask property) will cause the effect to fail.
 
 明智的蒙版 - 在离屏渲染上，蒙版具有与非透明视图类似的语义。 应用于 UIVisualEffectView 本身的蒙版被转发到所有内部视图，包括contentView。 你可以自由地将mask应用于contentView。 您提供给UIVisualEffectView的掩码将不是实际执行掩码的视图。 UIKit将复制视图以应用于每个子视图。 要将大小更改反映到蒙版，必须将更改应用到原始蒙版视图，并将其重置到效果视图上。 将遮罩应用于UIVisualEffectView（通过setMaskView：或图层的遮罩属性）的超级视图将导致该效果失败。

 • Correctly capturing snapshots - Many effects require support from the window that hosts the view. As such, attempting to take a snapshot of just the UIVisualEffectView will result in the snapshot not containing the effect at all or it appearing incorrectly. To properly snapshot a view hierarchy that contains a UIVisualEffectView, you must snapshot the entire UIWindow or UIScreen that contains it.
 
 正确捕获快照 - 许多影响需要托管视图的窗口支持。 因此，尝试仅拍摄UIVisualEffectView的快照将导致快照不包含任何效果，或者显示不正确。 要正确地快照包含UIVisualEffectView的视图层次结构，必须对包含它的整个UIWindow或UIScreen进行快照。
 */

NS_CLASS_AVAILABLE_IOS(8_0) @interface UIVisualEffectView : UIView <NSSecureCoding>

@property (nonatomic, strong, readonly) UIView *contentView; // Do not add subviews directly to UIVisualEffectView, use this view instead.

@property (nonatomic, copy, nullable) UIVisualEffect *effect;

- (instancetype)initWithEffect:(nullable UIVisualEffect *)effect NS_DESIGNATED_INITIALIZER;
- (nullable instancetype)initWithCoder:(NSCoder *)aDecoder NS_DESIGNATED_INITIALIZER;

@end

NS_ASSUME_NONNULL_END

```

### 示例代码一
```
// 添加毛玻璃效果 
UIVisualEffect *blurEffect = [UIBlurEffect effectWithStyle:UIBlurEffectStyleDark];
UIVisualEffectView *visualEffectView = [[UIVisualEffectView alloc] initWithEffect:blurEffect];
[visualEffectView setFrame:self.view.bounds];
[visualEffectView.contentView addSubview:_loginView];
[self.view addSubview:visualEffectView];
```

### 示例代码二
```objective-c
- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view, typically from a nib.
    
    // 创建图片视图
    UIImageView *imageView = [[UIImageView alloc] initWithImage:[UIImage imageNamed:@"unsplash_Kyle_Sterk.png"]];
    imageView.contentMode = UIViewContentModeScaleAspectFill;
    imageView.frame = self.view.bounds;
    imageView.alpha = 0.5;
    [self.view addSubview:imageView];
    
    // Blur effect 模糊效果
    UIBlurEffect *blurEffect = [UIBlurEffect effectWithStyle:UIBlurEffectStyleLight];
    UIVisualEffectView *blurEffectView = [[UIVisualEffectView alloc] initWithEffect:blurEffect];
    blurEffectView.frame = self.view.bounds;
    [self.view addSubview:blurEffectView];
    
    // Vibrancy effect 生动效果
    UIVibrancyEffect *vibrancyEffect = [UIVibrancyEffect effectForBlurEffect:blurEffect];
    UIVisualEffectView *vibrancyEffectView = [[UIVisualEffectView alloc] initWithEffect:vibrancyEffect];
    vibrancyEffectView.frame = self.view.bounds;

    // 效果字体
    UILabel *label = [[UILabel alloc] init];
    label.text = @"Stay Hungry,Stay Foolish.";//
    label.font = [UIFont fontWithName:@"Sacramento-Regular" size:32.0f];
    [label sizeToFit];
    label.center = self.view.center;
    
    // 添加标签到 vibrancy effect view 的 contentView 上
    
    [vibrancyEffectView.contentView addSubview:label];
    
    // 添加 UIVisualEffectView 到 Blur effect view 的 contentView 上
    [blurEffectView.contentView addSubview:vibrancyEffectView];
    
    // [blurEffectView.contentView addSubview:label];
}
```

## ![](http://upload-images.jianshu.io/upload_images/2648731-9ddc08275ff72b4f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 参考
- [【译】UIVisualEffectView 教程](https://juejin.im/post/6844903568667836423)
