* GitHub源码: [MBProgressHUD](https://github.com/jdg/MBProgressHUD)
* star: 13000+

以下内容来源于官方源码、 README 文档、测试 Demo或个人使用总结 ！

# MBProgressHUD

**MBProgressHUD** 是一个 iOS 的半透明的指示器或标签组件，用于显示后台线程的进度。**MBProgressHUD** 比没有文档的、私有的 **UIKit UIProgressHUD** 多了一些特性，旨在取而代之。

![](http://upload-images.jianshu.io/upload_images/2648731-2456015ac10ca0a3.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



## Requirements

**MBProgressHUD** 需要使用的Apple框架：

* Foundation.framework
* UIKit.framework
* CoreGraphics.framework



## 使用

使用 **MBProgressHUD** 最重要的一点是：当执行需要较长时间的任务时，保证主线程是空闲的，这样可以使UI实时更新。因此： 推荐的做法是在主线程中使用 **MBProgressHUD**，把其他你想要执行的任务分发到其他的线程里:

```objectivec
[MBProgressHUD showHUDAddedTo:self.view animated:YES];
dispatch_async(dispatch_get_global_queue( DISPATCH_QUEUE_PRIORITY_LOW, 0), ^{
	// Do something...
	dispatch_async(dispatch_get_main_queue(), ^{
		[MBProgressHUD hideHUDForView:self.view animated:YES];
	});
});
```

你可以在任何视图或窗口中添加HUD。 但是，请尽量避免将HUD添加到具有复杂视图层次结构的某些 **UIKit** 视图（如 **UITableView** 或 **UICollectionView** ）中。 那些视图可以以意想不到的方式变换他们的子视图，从而影响 HUD的显示。

如果你想配置 HUD,你可以使用由  `showHUDAddedTo:animated: ` 方法返回的 **MBProgressHUD** 的实例.

```objectivec
MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.view animated:YES];
hud.mode = MBProgressHUDModeAnnularDeterminate;
hud.labelText = @"Loading";
[self doSomethingInBackgroundWithProgressCallback:^(float progress) {
	hud.progress = progress;
} completionCallback:^{
	[hud hide:YES];
}];
```

你也可以使用 **NSProgress** 对象，当通过该对象报告进度时，**MBProgressHUD** 将自动更新。

```objectivec
MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.view animated:YES];
hud.mode = MBProgressHUDModeAnnularDeterminate;
hud.labelText = @"Loading";
NSProgress *progress = [self doSomethingInBackgroundCompletion:^{
	[hud hide:YES];
}];
hud.progressObject = progress;
```

UI 更新应该通常总是在主线程完成。但是某些 **MBProgressHUD** 设置器,考虑到了**线程安全** ,并且可以在后台线程里被调用。这些设置器,具体指的是: `setMode:`,`setCustomView:`, `setLabelText:`, `setLabelFont:`, `setDetailsLabelText:`,`setDetailsLabelFont:` 和 `setProgress:`。

如果你需要在主线程执行需要长时间运行的任务,你应该在短暂的延迟后再执行这个任务,这样在你的任务阻塞主线程之前, UIKit 就有足够的时间来更新UI(如,绘制HUD).

```objectivec
[MBProgressHUD showHUDAddedTo:self.view animated:YES];
// 延迟 0.01s 后执行
dispatch_time_t popTime = dispatch_time(DISPATCH_TIME_NOW, 0.01 * NSEC_PER_SEC);
dispatch_after(popTime, dispatch_get_main_queue(), ^(void){
	// Do something...
	[MBProgressHUD hideHUDForView:self.view animated:YES];
});
```

你应该知道，在 Block块完成之前，上述 Block块中发出的任何 HUD 更新消息将不会被显示。





# 源码

```objectivec
//
//  MBProgressHUD.h
//  Version 1.0.0
//  Created by Matej Bukovinski on 2.4.09.
//

// This code is distributed under the terms and conditions of the MIT license. 

// Copyright © 2009-2016 Matej Bukovinski
//
// Permission is hereby granted, free of charge, to any person obtaining a copy
// of this software and associated documentation files (the "Software"), to deal
// in the Software without restriction, including without limitation the rights
// to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
// copies of the Software, and to permit persons to whom the Software is
// furnished to do so, subject to the following conditions:
//
// The above copyright notice and this permission notice shall be included in
// all copies or substantial portions of the Software.
//
// THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
// IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
// FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
// AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
// LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
// OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
// THE SOFTWARE.

#import <Foundation/Foundation.h>
#import <UIKit/UIKit.h>
#import <CoreGraphics/CoreGraphics.h>

@class MBBackgroundView;
@protocol MBProgressHUDDelegate;


extern CGFloat const MBProgressMaxOffset;

typedef NS_ENUM(NSInteger, MBProgressHUDMode) {
    /// UIActivityIndicatorView.
    MBProgressHUDModeIndeterminate,
    /// 一个圆形的饼图，像进度视图。
    MBProgressHUDModeDeterminate,
    /// 水平进度条。
    MBProgressHUDModeDeterminateHorizontalBar,
    /// 环形进度视图。
    MBProgressHUDModeAnnularDeterminate,
    /// 显示自定义视图。
    MBProgressHUDModeCustomView,
    /// 仅显示标签。
    MBProgressHUDModeText
};

typedef NS_ENUM(NSInteger, MBProgressHUDAnimation) {
    /// 不透明动画
    MBProgressHUDAnimationFade,
    /// 不透明+缩放动画（显示时缩小，消失时放大）
    MBProgressHUDAnimationZoom,
    /// Opacity + scale animation (zoom out style)
    MBProgressHUDAnimationZoomOut,
    /// Opacity + scale animation (zoom in style)
    MBProgressHUDAnimationZoomIn
};

typedef NS_ENUM(NSInteger, MBProgressHUDBackgroundStyle) {
    /// 纯色背景
    MBProgressHUDBackgroundStyleSolidColor,
    /// UIVisualEffectView 或 UIToolbar.layer 背景视图
    MBProgressHUDBackgroundStyleBlur
};

typedef void (^MBProgressHUDCompletionBlock)();


NS_ASSUME_NONNULL_BEGIN


/** 
 * Displays a simple HUD window containing a progress indicator and two optional labels for short messages.
 *
 * This is a simple drop-in class for displaying a progress HUD view similar to Apple's private UIProgressHUD class.
 * The MBProgressHUD window spans over the entire space given to it by the initWithFrame: constructor and catches all
 * user input on this region, thereby preventing the user operations on components below the view.
 *
 * @note To still allow touches to pass through the HUD, you can set hud.userInteractionEnabled = NO.
 * @attention MBProgressHUD is a UI class and should therefore only be accessed on the main thread.
 */
@interface MBProgressHUD : UIView

/**
 * 创建一个新的HUD,并把它添加并显示到提供的视图上.与之相对应的方法是 hideHUDForView:animated:.
 *
 * @note 这个方法会设置HUD的属性 removeFromSuperViewOnHide 为YES.HUD会在隐藏时自动从父视图上移除.
 *
 * @param view 将HUD添加到此视图上.
 * @param animated YES:显示时使用当前的动画类型显示;NO:直接显示不使用动画效果.
 * @return A reference to the created HUD.
 *
 * @see hideHUDForView:animated:
 * @see animationType
 */
+ (instancetype)showHUDAddedTo:(UIView *)view animated:(BOOL)animated;

/// @name Showing and hiding

/**
 * 找到并隐藏子视图最顶层的HUD.与之对应的方法是 showHUDAddedTo:animated:.
 
 *
 * @note 这个方法会设置HUD的属性 removeFromSuperViewOnHide 为YES.HUD会在隐藏时自动从父视图上移除.
 *
 * @param view  用来在其子视图中查找HUD的视图.
 * @param animated YES,隐藏时使用当前的动画类型;NO,直接隐藏不使用动画效果.
 * @return  如果某个HUD被找到并被移除;否则返回NO.
 *
 * @see showHUDAddedTo:animated:
 * @see animationType
 */
+ (BOOL)hideHUDForView:(UIView *)view animated:(BOOL)animated;

/**
 * 找到最顶层的 HUD 并返回.
 *
 * @param view The view that is going to be searched.
 * @return A reference to the last HUD subview discovered.
 */
+ (nullable MBProgressHUD *)HUDForView:(UIView *)view;

/**
 * 使用指定视图的边框尺寸创建HUD的便利初始化方法. Calls the designated constructor with
 * view.bounds as the parameter.
 *
 * @param view The view instance that will provide the bounds for the HUD. Should be the same instance as
 * the HUD's superview (i.e., the view that the HUD will be added to).
 */
- (instancetype)initWithView:(UIView *)view;

/** 
 * 显示 HUD.
 *
 * @note 你需要确保调用此方法时,主线程未被其他任务阻塞,以便于更新视图.当你已经在新的线程中开始(比如,使用NSOperation或者NSURLRequest的异步请求等)执行某个任务后,再执行此方法.
 * (e.g., when using something like NSOperation or making an asynchronous call like NSURLRequest).
 * @param animated If set to YES the HUD will appear using the current animationType. If set to NO the HUD will not use
 * animations while appearing.
 *
 * @see animationType
 */
- (void)showAnimated:(BOOL)animated;

/** 
 * Hides the HUD. This still calls the hudWasHidden: delegate. This is the counterpart of the show: method. Use it to
 * hide the HUD when your task completes.
 *
 * @param animated If set to YES the HUD will disappear using the current animationType. If set to NO the HUD will not use
 * animations while disappearing.
 *
 * @see animationType
 */
- (void)hideAnimated:(BOOL)animated;

/** 
 * 指定延时后隐藏 HUD . This still calls the hudWasHidden: delegate. This is the counterpart of the show: method. Use it to
 * hide the HUD when your task completes.
 *
 * @param animated If set to YES the HUD will disappear using the current animationType. If set to NO the HUD will not use
 * animations while disappearing.
 * @param delay Delay in seconds until the HUD is hidden.
 *
 * @see animationType
 */
- (void)hideAnimated:(BOOL)animated afterDelay:(NSTimeInterval)delay;

/**
 * The HUD delegate object. Receives HUD state notifications.
 */
@property (weak, nonatomic) id<MBProgressHUDDelegate> delegate;

/**
 * HUD完全隐藏后执行的block.
 */
@property (copy, nullable) MBProgressHUDCompletionBlock completionBlock;

/*
 * 用于指定某个方法执行一段时间之后再显示HUD,以秒记.如果方法在这段时间之前就执行完成,就不再显示HUD.
 * This may be used to prevent HUD display for very short tasks.
 * Defaults to 0 (no grace time).
 */
@property (assign, nonatomic) NSTimeInterval graceTime;

/**
 * HUD显示的最小时间(以秒记.)
 * This avoids the problem of the HUD being shown and than instantly hidden.
 * Defaults to 0 (no minimum show time).
 */
@property (assign, nonatomic) NSTimeInterval minShowTime;

/**
 * Removes the HUD from its parent view when hidden.
 * Defaults to NO.
 */
@property (assign, nonatomic) BOOL removeFromSuperViewOnHide;

/// @name Appearance

/** 
 * MBProgressHUD operation mode. The default is MBProgressHUDModeIndeterminate.
 */
@property (assign, nonatomic) MBProgressHUDMode mode;

/**
 * A color that gets forwarded to all labels and supported indicators. Also sets the tintColor
 * for custom views on iOS 7+. Set to nil to manage color individually.
 * Defaults to semi-translucent black on iOS 7 and later and white on earlier iOS versions.
 */
@property (strong, nonatomic, nullable) UIColor *contentColor UI_APPEARANCE_SELECTOR;

/**
 * HUD显示和隐藏时使用的动画类型.
 */
@property (assign, nonatomic) MBProgressHUDAnimation animationType UI_APPEARANCE_SELECTOR;

/**
 * The bezel offset relative to the center of the view. You can use MBProgressMaxOffset
 * and -MBProgressMaxOffset to move the HUD all the way to the screen edge in each direction.
 * E.g., CGPointMake(0.f, MBProgressMaxOffset) would position the HUD centered on the bottom edge.
 */
@property (assign, nonatomic) CGPoint offset UI_APPEARANCE_SELECTOR;

/**
 * The amount of space between the HUD edge and the HUD elements (labels, indicators or custom views).
 * This also represents the minimum bezel distance to the edge of the HUD view.
 * Defaults to 20.f
 */
@property (assign, nonatomic) CGFloat margin UI_APPEARANCE_SELECTOR;

/**
 * The minimum size of the HUD bezel. Defaults to CGSizeZero (no minimum size).
 */
@property (assign, nonatomic) CGSize minSize UI_APPEARANCE_SELECTOR;

/**
 * Force the HUD dimensions to be equal if possible.
 */
@property (assign, nonatomic, getter = isSquare) BOOL square UI_APPEARANCE_SELECTOR;

/**
 * When enabled, the bezel center gets slightly affected by the device accelerometer data.
 * Has no effect on iOS < 7.0. Defaults to YES.
 */
@property (assign, nonatomic, getter=areDefaultMotionEffectsEnabled) BOOL defaultMotionEffectsEnabled UI_APPEARANCE_SELECTOR;

/// @name Progress

/**
 * The progress of the progress indicator, from 0.0 to 1.0. Defaults to 0.0.
 */
@property (assign, nonatomic) float progress;

/// @name ProgressObject

/**
 * The NSProgress object feeding the progress information to the progress indicator.
 */
@property (strong, nonatomic, nullable) NSProgress *progressObject;

/// @name Views

/**
 * The view containing the labels and indicator (or customView).
 */
@property (strong, nonatomic, readonly) MBBackgroundView *bezelView;

/**
 * View covering the entire HUD area, placed behind bezelView.
 */
@property (strong, nonatomic, readonly) MBBackgroundView *backgroundView;

/**
 * The UIView (e.g., a UIImageView) to be shown when the HUD is in MBProgressHUDModeCustomView.
 * The view should implement intrinsicContentSize for proper sizing. For best results use approximately 37 by 37 pixels.
 */
@property (strong, nonatomic, nullable) UIView *customView;

/**
 * A label that holds an optional short message to be displayed below the activity indicator. The HUD is automatically resized to fit
 * the entire text.
 */
@property (strong, nonatomic, readonly) UILabel *label;

/**
 * A label that holds an optional details message displayed below the labelText message. The details text can span multiple lines.
 */
@property (strong, nonatomic, readonly) UILabel *detailsLabel;

/**
 * A button that is placed below the labels. Visible only if a target / action is added. 
 */
@property (strong, nonatomic, readonly) UIButton *button;

@end


@protocol MBProgressHUDDelegate <NSObject>

@optional

/** 
 * Called after the HUD was fully hidden from the screen. 
 */
- (void)hudWasHidden:(MBProgressHUD *)hud;

@end


/**
 * A progress view for showing definite progress by filling up a circle (pie chart).
 */
@interface MBRoundProgressView : UIView 

/**
 * Progress (0.0 to 1.0)
 */
@property (nonatomic, assign) float progress;

/**
 * Indicator progress color.
 * Defaults to white [UIColor whiteColor].
 */
@property (nonatomic, strong) UIColor *progressTintColor;

/**
 * Indicator background (non-progress) color. 
 * Only applicable on iOS versions older than iOS 7.
 * Defaults to translucent white (alpha 0.1).
 */
@property (nonatomic, strong) UIColor *backgroundTintColor;

/*
 * Display mode - NO = round or YES = annular. Defaults to round.
 */
@property (nonatomic, assign, getter = isAnnular) BOOL annular;

@end


/**
 * A flat bar progress view. 
 */
@interface MBBarProgressView : UIView

/**
 * Progress (0.0 to 1.0)
 */
@property (nonatomic, assign) float progress;

/**
 * Bar border line color.
 * Defaults to white [UIColor whiteColor].
 */
@property (nonatomic, strong) UIColor *lineColor;

/**
 * Bar background color.
 * Defaults to clear [UIColor clearColor];
 */
@property (nonatomic, strong) UIColor *progressRemainingColor;

/**
 * Bar progress color.
 * Defaults to white [UIColor whiteColor].
 */
@property (nonatomic, strong) UIColor *progressColor;

@end


@interface MBBackgroundView : UIView

/**
 * The background style. 
 * Defaults to MBProgressHUDBackgroundStyleBlur on iOS 7 or later and MBProgressHUDBackgroundStyleSolidColor otherwise.
 * @note Due to iOS 7 not supporting UIVisualEffectView, the blur effect differs slightly between iOS 7 and later versions.
 */
@property (nonatomic) MBProgressHUDBackgroundStyle style;

/**
 * The background color or the blur tint color.
 * @note Due to iOS 7 not supporting UIVisualEffectView, the blur effect differs slightly between iOS 7 and later versions.
 */
@property (nonatomic, strong) UIColor *color;

@end

@interface MBProgressHUD (Deprecated)

+ (NSArray *)allHUDsForView:(UIView *)view __attribute__((deprecated("Store references when using more than one HUD per view.")));
+ (NSUInteger)hideAllHUDsForView:(UIView *)view animated:(BOOL)animated __attribute__((deprecated("Store references when using more than one HUD per view.")));

- (id)initWithWindow:(UIWindow *)window __attribute__((deprecated("Use initWithView: instead.")));

- (void)show:(BOOL)animated __attribute__((deprecated("Use showAnimated: instead.")));
- (void)hide:(BOOL)animated __attribute__((deprecated("Use hideAnimated: instead.")));
- (void)hide:(BOOL)animated afterDelay:(NSTimeInterval)delay __attribute__((deprecated("Use hideAnimated:afterDelay: instead.")));

- (void)showWhileExecuting:(SEL)method onTarget:(id)target withObject:(id)object animated:(BOOL)animated __attribute__((deprecated("Use GCD directly.")));
- (void)showAnimated:(BOOL)animated whileExecutingBlock:(dispatch_block_t)block __attribute__((deprecated("Use GCD directly.")));
- (void)showAnimated:(BOOL)animated whileExecutingBlock:(dispatch_block_t)block completionBlock:(nullable MBProgressHUDCompletionBlock)completion __attribute__((deprecated("Use GCD directly.")));
- (void)showAnimated:(BOOL)animated whileExecutingBlock:(dispatch_block_t)block onQueue:(dispatch_queue_t)queue __attribute__((deprecated("Use GCD directly.")));
- (void)showAnimated:(BOOL)animated whileExecutingBlock:(dispatch_block_t)block onQueue:(dispatch_queue_t)queue
     completionBlock:(nullable MBProgressHUDCompletionBlock)completion __attribute__((deprecated("Use GCD directly.")));
@property (assign) BOOL taskInProgress __attribute__((deprecated("No longer needed.")));

@property (nonatomic, copy) NSString *labelText __attribute__((deprecated("Use label.text instead.")));
@property (nonatomic, strong) UIFont *labelFont __attribute__((deprecated("Use label.font instead.")));
@property (nonatomic, strong) UIColor *labelColor __attribute__((deprecated("Use label.textColor instead.")));
@property (nonatomic, copy) NSString *detailsLabelText __attribute__((deprecated("Use detailsLabel.text instead.")));
@property (nonatomic, strong) UIFont *detailsLabelFont __attribute__((deprecated("Use detailsLabel.font instead.")));
@property (nonatomic, strong) UIColor *detailsLabelColor __attribute__((deprecated("Use detailsLabel.textColor instead.")));
@property (assign, nonatomic) CGFloat opacity __attribute__((deprecated("Customize bezelView properties instead.")));
@property (strong, nonatomic) UIColor *color __attribute__((deprecated("Customize the bezelView color instead.")));
@property (assign, nonatomic) CGFloat xOffset __attribute__((deprecated("Set offset.x instead.")));
@property (assign, nonatomic) CGFloat yOffset __attribute__((deprecated("Set offset.y instead.")));
@property (assign, nonatomic) CGFloat cornerRadius __attribute__((deprecated("Set bezelView.layer.cornerRadius instead.")));
@property (assign, nonatomic) BOOL dimBackground __attribute__((deprecated("Customize HUD background properties instead.")));
@property (strong, nonatomic) UIColor *activityIndicatorColor __attribute__((deprecated("Use UIAppearance to customize UIActivityIndicatorView. E.g.: [UIActivityIndicatorView appearanceWhenContainedIn:[MBProgressHUD class], nil].color = [UIColor redColor];")));
@property (atomic, assign, readonly) CGSize size __attribute__((deprecated("Get the bezelView.frame.size instead.")));

@end

NS_ASSUME_NONNULL_END
```





# Demo



### 1.1 不确定模式

![](http://upload-images.jianshu.io/upload_images/2648731-aaac5e1a931c5349.gif?imageMogr2/auto-orient/strip)

```objectivec
- (void)indeterminateExample {
    // 在根视图上显示HUD（self.view 是一个可滚动的列表视图，因此不适合将 HUD 随着用户划动与内容一起移动）。
    MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.navigationController.view animated:YES];

    // 激发异步任务，使 UIKit 有机会重新绘制HUD添加到视图层次结构。
    dispatch_async(dispatch_get_global_queue(QOS_CLASS_USER_INITIATED, 0), ^{

        // Do something useful in the background
        [self doSomeWork];

        // 重要 - 分配回主线程。 始终在主线程上访问UI类（包括MBProgressHUD）。
        dispatch_async(dispatch_get_main_queue(), ^{
            [hud hideAnimated:YES];
        });
    });
}
```



### 1.2 带标签

![](http://upload-images.jianshu.io/upload_images/2648731-3ba3383472bd474c.gif?imageMogr2/auto-orient/strip)

```objectivec
- (void)labelExample {
    MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.navigationController.view animated:YES];

    // 设置标签文本.
    hud.label.text = NSLocalizedString(@"Loading...", @"HUD loading title");
    // You can also adjust other label properties if needed.
    // hud.label.font = [UIFont italicSystemFontOfSize:16.f];

    dispatch_async(dispatch_get_global_queue(QOS_CLASS_USER_INITIATED, 0), ^{
        [self doSomeWork];
        dispatch_async(dispatch_get_main_queue(), ^{
            [hud hideAnimated:YES];
        });
    });
}
```





### 1.3 带详细标签

![](http://upload-images.jianshu.io/upload_images/2648731-6f32f1c967a84d20.gif?imageMogr2/auto-orient/strip)

```objectivec
- (void)detailsLabelExample {
    MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.navigationController.view animated:YES];

    // Set the label text.
    hud.label.text = NSLocalizedString(@"Loading...", @"HUD loading title");
    // Set the details label text. Let's make it multiline this time.
    hud.detailsLabel.text = NSLocalizedString(@"Parsing data\n(1/1)", @"HUD title");

    dispatch_async(dispatch_get_global_queue(QOS_CLASS_USER_INITIATED, 0), ^{
        [self doSomeWork];
        dispatch_async(dispatch_get_main_queue(), ^{
            [hud hideAnimated:YES];
        });
    });
}
```



### 2.1 确定模式

![](http://upload-images.jianshu.io/upload_images/2648731-9565cd47d90dd2e1.gif?imageMogr2/auto-orient/strip)

```objectivec
- (void)determinateExample {
    MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.navigationController.view animated:YES];

    // Set the determinate mode to show task progress.
    // 一个圆形的饼图
    hud.mode = MBProgressHUDModeDeterminate;
    hud.label.text = NSLocalizedString(@"Loading...", @"HUD loading title");

    dispatch_async(dispatch_get_global_queue(QOS_CLASS_USER_INITIATED, 0), ^{
        // Do something useful in the background and update the HUD periodically.
        [self doSomeWorkWithProgress];
        dispatch_async(dispatch_get_main_queue(), ^{
            [hud hideAnimated:YES];
        });
    });
}

- (void)doSomeWorkWithProgress {
    self.canceled = NO;
    // This just increases the progress indicator in a loop.
    float progress = 0.0f;
    while (progress < 1.0f) {
        if (self.canceled) break;
        progress += 0.01f;
        dispatch_async(dispatch_get_main_queue(), ^{
            // Instead we could have also passed a reference to the HUD
            // to the HUD to myProgressTask as a method parameter.
            [MBProgressHUD HUDForView:self.navigationController.view].progress = progress;
        });
        usleep(50000);
    }
}
```



### 2.2 环形确定模式

![](http://upload-images.jianshu.io/upload_images/2648731-0b44664c0fdf113e.gif?imageMogr2/auto-orient/strip)

```objectivec
- (void)annularDeterminateExample {
    MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.navigationController.view animated:YES];

    // Set the annular determinate mode to show task progress.
    hud.mode = MBProgressHUDModeAnnularDeterminate;
    hud.label.text = NSLocalizedString(@"Loading...", @"HUD loading title");

    dispatch_async(dispatch_get_global_queue(QOS_CLASS_USER_INITIATED, 0), ^{
        // Do something useful in the background and update the HUD periodically.
        [self doSomeWorkWithProgress];
        dispatch_async(dispatch_get_main_queue(), ^{
            [hud hideAnimated:YES];
        });
    });
}
```



### 2.3 条形确定模式

![](http://upload-images.jianshu.io/upload_images/2648731-8c74f0dfbc8fe596.gif?imageMogr2/auto-orient/strip)

```objectivec
- (void)barDeterminateExample {
    MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.navigationController.view animated:YES];

    // Set the bar determinate mode to show task progress.
    hud.mode = MBProgressHUDModeDeterminateHorizontalBar;
    hud.label.text = NSLocalizedString(@"Loading...", @"HUD loading title");

    dispatch_async(dispatch_get_global_queue(QOS_CLASS_USER_INITIATED, 0), ^{
        // Do something useful in the background and update the HUD periodically.
        [self doSomeWorkWithProgress];
        dispatch_async(dispatch_get_main_queue(), ^{
            [hud hideAnimated:YES];
        });
    });
}
```



### 3.1仅显示文本

![](http://upload-images.jianshu.io/upload_images/2648731-dfee72018c87bcc1.gif?imageMogr2/auto-orient/strip)

```objectivec
- (void)textExample {
    MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.navigationController.view animated:YES];

    // Set the text mode to show only text.
    hud.mode = MBProgressHUDModeText;
    hud.label.text = NSLocalizedString(@"Message here!", @"HUD message title");
    // 移动到底部中心
    hud.offset = CGPointMake(0.f, MBProgressMaxOffset);
	// 3秒后自动消失
    [hud hideAnimated:YES afterDelay:3.f];
}
```

#### Tips
属性 `bezelView` 可以修改 HUD 窗口颜色，该视图属性仅包含标签和指示器（或customView）。
而 `backgroundView` 属性：
> View covering the entire HUD area, placed behind bezelView

```objectivec
// 设置文字为白色
hud.label.textColor = [UIColor whiteColor];   
// 设置 HUD窗口为黑色
hud.bezelView.blurEffectStyle = UIBlurEffectStyleDark;
```


### 3.2 自定义视图

![](http://upload-images.jianshu.io/upload_images/2648731-c0d1216667ea5667.gif?imageMogr2/auto-orient/strip)

```objectivec
- (void)customViewExample {
    MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.navigationController.view animated:YES];

    // Set the custom view mode to show any view.
    hud.mode = MBProgressHUDModeCustomView;
    // Set an image view with a checkmark.
    UIImage *image = [[UIImage imageNamed:@"Checkmark"] imageWithRenderingMode:UIImageRenderingModeAlwaysTemplate];
    hud.customView = [[UIImageView alloc] initWithImage:image];
    // Looks a bit nicer if we make it square.
    hud.square = YES;
    // Optional label text.
    hud.label.text = NSLocalizedString(@"Done", @"HUD done title");

    [hud hideAnimated:YES afterDelay:3.f];
}
```



### 3.3 带按钮

![](http://upload-images.jianshu.io/upload_images/2648731-dc3fd6d8df1205c8.gif?imageMogr2/auto-orient/strip)

```objectivec
- (void)cancelationExample {
    MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.navigationController.view
                                              animated:YES];

    // Set the determinate mode to show task progress.
    hud.mode = MBProgressHUDModeDeterminate;
    hud.label.text = NSLocalizedString(@"Loading...", @"HUD loading title");

    // Configure the button.
    [hud.button setTitle:NSLocalizedString(@"Cancel", @"HUD cancel button title")
                forState:UIControlStateNormal];
    [hud.button addTarget:self
                   action:@selector(cancelWork:)
         forControlEvents:UIControlEventTouchUpInside];

    dispatch_async(dispatch_get_global_queue(QOS_CLASS_USER_INITIATED, 0), ^{
        // Do something useful in the background and update the HUD periodically.
        [self doSomeWorkWithProgress];
        dispatch_async(dispatch_get_main_queue(), ^{
            [hud hideAnimated:YES];
        });
    });
}

- (void)cancelWork:(id)sender {
    self.canceled = YES;
}

- (void)doSomeWorkWithProgress {
    self.canceled = NO;
    // This just increases the progress indicator in a loop.
    float progress = 0.0f;
    while (progress < 1.0f) {
        if (self.canceled) break;
        progress += 0.01f;
        dispatch_async(dispatch_get_main_queue(), ^{
            // Instead we could have also passed a reference to the HUD
            // to the HUD to myProgressTask as a method parameter.
            [MBProgressHUD HUDForView:self.navigationController.view].progress = progress;
        });
        usleep(50000);
    }
}
```



### 3.4 切换模式

![](http://upload-images.jianshu.io/upload_images/2648731-1840b1bac6e2e457.gif?imageMogr2/auto-orient/strip)

```objectivec
- (void)modeSwitchingExample {
    MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.navigationController.view
                                              animated:YES];

    // Set some text to show the initial status.
    hud.label.text = NSLocalizedString(@"Preparing...", @"HUD preparing title");
    // Will look best, if we set a minimum size.
    hud.minSize = CGSizeMake(150.f, 100.f);

    dispatch_async(dispatch_get_global_queue(QOS_CLASS_USER_INITIATED, 0), ^{
        // Do something useful in the background and update the HUD periodically.
        [self doSomeWorkWithMixedProgress];
        dispatch_async(dispatch_get_main_queue(), ^{
            [hud hideAnimated:YES];
        });
    });
}

- (void)doSomeWorkWithMixedProgress {
    // HUDForView: 找到顶层视图上的 HUD
    MBProgressHUD *hud = [MBProgressHUD HUDForView:self.navigationController.view];
    // 不确定模式
    sleep(2);
    // 异步主线程，切换到确定模式
    dispatch_async(dispatch_get_main_queue(), ^{
        hud.mode = MBProgressHUDModeDeterminate;
        hud.label.text = NSLocalizedString(@"Loading...", @"HUD loading title");
    });
    float progress = 0.0f;
    while (progress < 1.0f) {
        progress += 0.01f;
        dispatch_async(dispatch_get_main_queue(), ^{
            hud.progress = progress;
        });
        usleep(50000);
    }
    // 异步主线程，回到不确定模式
    dispatch_async(dispatch_get_main_queue(), ^{
        hud.mode = MBProgressHUDModeIndeterminate;
        hud.label.text = NSLocalizedString(@"Cleaning up...", @"HUD cleanining up title");
    });
    sleep(2);
    // 同步主线程，自定义视图
    dispatch_sync(dispatch_get_main_queue(), ^{
        UIImage *image = [[UIImage imageNamed:@"Checkmark"] imageWithRenderingMode:UIImageRenderingModeAlwaysTemplate];
        UIImageView *imageView = [[UIImageView alloc] initWithImage:image];
        hud.customView = imageView;
        hud.mode = MBProgressHUDModeCustomView;
        hud.label.text = NSLocalizedString(@"Completed", @"HUD completed title");
    });
    sleep(2);
}
```



### 4.1 显示在窗口



```objectivec
- (void)windowExample {
    // 覆盖整个屏幕。 类似于使用根视图控制器视图。
    MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.view.window animated:YES];
    dispatch_async(dispatch_get_global_queue(QOS_CLASS_USER_INITIATED, 0), ^{
        [self doSomeWork];
        dispatch_async(dispatch_get_main_queue(), ^{
            [hud hideAnimated:YES];
        });
    });
}
```



### 4.2 NSURLSession

![](http://upload-images.jianshu.io/upload_images/2648731-f8b86426ed22d8ed.gif?imageMogr2/auto-orient/strip)

```objectivec
- (void)networkingExample {
    MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.navigationController.view animated:YES];

    // Set some text to show the initial status.
    hud.label.text = NSLocalizedString(@"Preparing...", @"HUD preparing title");
    // Will look best, if we set a minimum size.
    hud.minSize = CGSizeMake(150.f, 100.f);

    [self doSomeNetworkWorkWithProgress];
}

- (void)doSomeNetworkWorkWithProgress {
    NSURLSessionConfiguration *sessionConfig = [NSURLSessionConfiguration defaultSessionConfiguration];
    NSURLSession *session = [NSURLSession sessionWithConfiguration:sessionConfig delegate:self delegateQueue:nil];
    NSURL *URL = [NSURL URLWithString:@"https://support.apple.com/library/APPLE/APPLECARE_ALLGEOS/HT1425/sample_iPod.m4v.zip"];
    NSURLSessionDownloadTask *task = [session downloadTaskWithURL:URL];
    [task resume];
}

#pragma mark - NSURLSessionDelegate

- (void)URLSession:(NSURLSession *)session downloadTask:(NSURLSessionDownloadTask *)downloadTask didFinishDownloadingToURL:(NSURL *)location {
    // Do something with the data at location...

    // Update the UI on the main thread
    dispatch_async(dispatch_get_main_queue(), ^{
        MBProgressHUD *hud = [MBProgressHUD HUDForView:self.navigationController.view];
        UIImage *image = [[UIImage imageNamed:@"Checkmark"] imageWithRenderingMode:UIImageRenderingModeAlwaysTemplate];
        UIImageView *imageView = [[UIImageView alloc] initWithImage:image];
        hud.customView = imageView;
        hud.mode = MBProgressHUDModeCustomView;
        hud.label.text = NSLocalizedString(@"Completed", @"HUD completed title");
        [hud hideAnimated:YES afterDelay:3.f];
    });
}

- (void)URLSession:(NSURLSession *)session downloadTask:(NSURLSessionDownloadTask *)downloadTask didWriteData:(int64_t)bytesWritten totalBytesWritten:(int64_t)totalBytesWritten totalBytesExpectedToWrite:(int64_t)totalBytesExpectedToWrite {
    float progress = (float)totalBytesWritten / (float)totalBytesExpectedToWrite;

    // 在主线程更新 UI 进度条
    dispatch_async(dispatch_get_main_queue(), ^{
        MBProgressHUD *hud = [MBProgressHUD HUDForView:self.navigationController.view];
        hud.mode = MBProgressHUDModeDeterminate;
        hud.progress = progress;
    });
}
```



### 4.3 带进度条的确定模式

![](http://upload-images.jianshu.io/upload_images/2648731-2f103854819cf4ae.gif?imageMogr2/auto-orient/strip)

```objectivec
- (void)determinateNSProgressExample {
    MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.navigationController.view animated:YES];
    
    // Set the determinate mode to show task progress.
    hud.mode = MBProgressHUDModeDeterminate;
    hud.label.text = NSLocalizedString(@"Loading...", @"HUD loading title");
    
    // Set up NSProgress
    NSProgress *progressObject = [NSProgress progressWithTotalUnitCount:100];
    hud.progressObject = progressObject;
    
    // Configure a cancel button.
    [hud.button setTitle:NSLocalizedString(@"Cancel", @"HUD cancel button title") forState:UIControlStateNormal];
    [hud.button addTarget:progressObject action:@selector(cancel) forControlEvents:UIControlEventTouchUpInside];
    
    dispatch_async(dispatch_get_global_queue(QOS_CLASS_USER_INITIATED, 0), ^{
        // Do something useful in the background and update the HUD periodically.
        [self doSomeWorkWithProgressObject:progressObject];
        dispatch_async(dispatch_get_main_queue(), ^{
            [hud hideAnimated:YES];
        });
    });
}
```



### 4.4 Dim background

![](http://upload-images.jianshu.io/upload_images/2648731-fa4ddf2e441f5e1e.gif?imageMogr2/auto-orient/strip)

```objectivec
- (void)dimBackgroundExample {
    MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.navigationController.view animated:YES];
    
    // Change the background view style and color.
    hud.backgroundView.style = MBProgressHUDBackgroundStyleSolidColor;
    hud.backgroundView.color = [UIColor colorWithWhite:0.f alpha:0.1f];
    
    dispatch_async(dispatch_get_global_queue(QOS_CLASS_USER_INITIATED, 0), ^{
        [self doSomeWork];
        dispatch_async(dispatch_get_main_queue(), ^{
            [hud hideAnimated:YES];
        });
    });
}
```



#### MBProgressHUDBackgroundStyle

```objectivec
typedef NS_ENUM(NSInteger, MBProgressHUDBackgroundStyle) {
    /// 纯色背景
    MBProgressHUDBackgroundStyleSolidColor,
    /// UIVisualEffectView 或 UIToolbar.layer 背景视图
    MBProgressHUDBackgroundStyleBlur
};
```

背景视图样式有2种：

**MBProgressHUDBackgroundStyleSolidColor**:

![](http://upload-images.jianshu.io/upload_images/2648731-f0b5f9ab6a90b667.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



**MBProgressHUDBackgroundStyleBlur**

![](http://upload-images.jianshu.io/upload_images/2648731-dc8db0f7922da918.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### 4.5 Colored

![](http://upload-images.jianshu.io/upload_images/2648731-34fbc38174672799.gif?imageMogr2/auto-orient/strip)

```objectivec
- (void)colorExample {
	MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.navigationController.view
                                              animated:YES];
	hud.contentColor = [UIColor colorWithRed:0.f green:0.6f blue:0.7f alpha:1.f];

	// Set the label text.
	hud.label.text = NSLocalizedString(@"Loading...", @"HUD loading title");

	dispatch_async(dispatch_get_global_queue(QOS_CLASS_USER_INITIATED, 0), ^{
		[self doSomeWork];
		dispatch_async(dispatch_get_main_queue(), ^{
			[hud hideAnimated:YES];
		});
	});
}
```


# 自定义的便捷扩展类

为了方便使用，我们可以通过自己封装一个 Category 扩展，以方便使用，下面附上我自己封装的类：

## MBProgressHUD+HQL.h

封装的便捷方法：
1. 显示成功 HUD，带 Image 和 Label，3 秒后自动消失。
2. 显示失败 HUD，带 Image 和 Label，3 秒后自动消失。
3. 显示文本标签，3 秒后自动消失。


```objectivec
#import <MBProgressHUD/MBProgressHUD.h>

@interface MBProgressHUD (HQL)

#pragma mark - Style 1:Image+Label
/**
 显示成功 HUD，带 Image 和 Label，3 秒后自动消失。

 @param text 描述信息
 @param view 显示HUD的视图
 */
+ (void)hql_showSuccessHUD:(NSString *)text toView:(UIView *)view;

/**
 在窗口显示成功 HUD，带 Image 和 Label，3 秒后自动消失。

 @param text 标签文字
 */
+ (void)hql_showSuccessHUD:(NSString *)text;

/**
 显示失败 HUD，带 Image 和 Label，3 秒后自动消失。

 @param text 描述信息
 @param view 显示HUD的视图
 */
+ (void)hql_showErrorHUD:(NSString *)text toView:(UIView *)view;

/**
 显示失败 HUD，带 Image 和 Label，3 秒后自动消失。

 @param text  描述信息
 */
+ (void)hql_showErrorHUD:(NSString *)text;


#pragma mark - Style 2:Label

/**
 中心显示提示标签,3 秒后自动消失

 @param message 描述信息
 @param view 显示HUD的视图
 */
+ (MBProgressHUD *)hql_showTextHUD:(NSString *)message toView:(UIView *)view;

/**
 在窗口显示提示标签 HUD,3 秒后自动消失

 @param message 描述信息
 */
+ (MBProgressHUD *)hql_showTextHUD:(NSString *)message;

#pragma mark - Hide

+ (void)hql_hideHUDForView:(UIView *)view;
+ (void)hql_hideHUD;

@end
```

## MBProgressHUD+HQL.m

```objectivec
#import "MBProgressHUD+HQL.h"

static const NSTimeInterval KAnimationDuration = 3.0;

@implementation MBProgressHUD (HQL)

#pragma mark - 显示自动隐藏 HUD：Image + Label

+ (void)showCustomHUDWithText:(NSString *)text
               WithImageNamed:(NSString *)name
                       toView:(UIView *)view
                   afterDelay:(NSTimeInterval)delay {
    if (!view) {
        view = [[UIApplication sharedApplication] keyWindow];
    }
    MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:view animated:YES];
    hud.mode = MBProgressHUDModeCustomView;
    hud.customView = [[UIImageView alloc] initWithImage:[UIImage imageNamed:name]];
    hud.label.text = [text isNotBlank] ? text : @"错误提示：返回数据为空";
    hud.minSize = CGSizeMake(150.f, 100.f);
    // dim background
    hud.backgroundView.style = MBProgressHUDBackgroundStyleSolidColor;
    hud.backgroundView.color = [UIColor colorWithWhite:0.f alpha:0.1f];
    [hud hideAnimated:YES afterDelay:delay];
}


#pragma mark -
#pragma mark 显示带ICON的成功/错误信息，自动消失
+ (void)hql_showSuccessHUD:(NSString *)text toView:(UIView *)view {
    [self showCustomHUDWithText:text
                 WithImageNamed:@"password_success"
                         toView:view
                     afterDelay:KAnimationDuration];
}

+ (void)hql_showSuccessHUD:(NSString *)text {
    [self hql_showSuccessHUD:text toView:nil];
}

+ (void)hql_showErrorHUD:(NSString *)text toView:(UIView *)view {
    [self showCustomHUDWithText:text
                 WithImageNamed:@"password_error"
                         toView:view
                     afterDelay:KAnimationDuration];
}

+ (void)hql_showErrorHUD:(NSString *)text {
    [self hql_showErrorHUD:text toView:nil];
}


#pragma mark - 显示提示标签
+ (MBProgressHUD *)hql_showTextHUD:(NSString *)message toView:(UIView *)view {
    if (!view)  {
        view = [[UIApplication sharedApplication] keyWindow];
    }
    MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:view animated:YES];
    hud.mode = MBProgressHUDModeText;
//    hud.label.text = message;
//    hud.label.textColor = [UIColor whiteColor];
    hud.detailsLabel.text = [message isNotBlank] ? message : @"错误提示：返回数据为空";
    hud.detailsLabel.textColor = [UIColor whiteColor];
    hud.bezelView.color = [[UIColor blackColor] colorWithAlphaComponent:0.8];
    // 3秒后自动消失
    [hud hideAnimated:YES afterDelay:KAnimationDuration];
    return hud;
}

#pragma mark 在窗口上显示提示信息
+ (MBProgressHUD *)hql_showTextHUD:(NSString *)message {
    return [self hql_showTextHUD:message toView:nil];
}

#pragma mark - 隐藏HUD
+ (void)hql_hideHUDForView:(UIView *)view {
    if (view == nil) {
        view = [[UIApplication sharedApplication] keyWindow];
    }
    [self hideHUDForView:view animated:YES];
}

+ (void)hql_hideHUD {
    [self hql_hideHUDForView:nil];
}

@end
```



### 参考：
- [MBProgressHUD：一个不错的进度提示工具 @伯乐在线](http://hao.jobbole.com/mbprogresshud-ios/)
- [iOS提示框,为什么你应该使用 MBProgressHUD? @iOS122](http://www.cnblogs.com/ios122/p/4817369.html)
- [iOS 简约加载动画详解](http://www.jianshu.com/p/3d62a6d9fdaf)
