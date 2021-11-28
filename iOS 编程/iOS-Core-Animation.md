## UIView 基本动画

```objective-c
// 通过指定动画持续时间、动画延迟、执行动画选项和动画完成后回调的 Block 对象 更改一个或多个视图的动画。
+ (void)animateWithDuration:(NSTimeInterval)duration delay:(NSTimeInterval)delay options:(UIViewAnimationOptions)options animations:(void (^)(void))animations completion:(void (^ __nullable)(BOOL finished))completion NS_AVAILABLE_IOS(4_0);

+ (void)animateWithDuration:(NSTimeInterval)duration animations:(void (^)(void))animations completion:(void (^ __nullable)(BOOL finished))completion NS_AVAILABLE_IOS(4_0); // delay = 0.0, options = 0

+ (void)animateWithDuration:(NSTimeInterval)duration animations:(void (^)(void))animations NS_AVAILABLE_IOS(4_0); // delay = 0.0, options = 0, completion = NULL
```



### 示例：平移动画

```objective-c
- (void)viewDidLoad {
    [super viewDidLoad];
    
    UIView *view1 = [[UIView alloc] initWithFrame:CGRectMake(22, 22, 100, 100)];
    view1.backgroundColor = [UIColor flatMintColor];
    [self.view addSubview:view1];
    
    [UIView animateWithDuration:2.0    // 动画持续时间
                          delay:1.0    // 动画执行的延迟时间
                        options:UIViewAnimationOptionLayoutSubviews    // 执行的动画选项
                     animations:^{
                         // 要执行的动画代码
                         view1.center = self.view.center;
                     }
                     completion:^(BOOL finished) {
                         // 动画执行完毕后的调用

                     }];
}
```

![](http://upload-images.jianshu.io/upload_images/2648731-10f6a9144dff63df.gif?imageMogr2/auto-orient/strip)

---



### 示例：重复动画

```objective-c
- (void)viewDidLoad {
    [super viewDidLoad];
    
    UIView *view1 = [[UIView alloc] initWithFrame:CGRectMake(22, 22, 100, 100)];
    view1.backgroundColor = [UIColor flatMintColor];
    [self.view addSubview:view1];
    
    [UIView animateWithDuration:3.0
                          delay:1.0
                        options:UIViewAnimationOptionRepeat |
                                UIViewAnimationOptionAutoreverse |
                                UIViewAnimationOptionCurveEaseInOut
                     animations:^{
                         // 要执行的动画代码
                         view1.center = self.view.center;
                         view1.backgroundColor = [UIColor flatSkyBlueColor];
                     }
                     completion:^(BOOL finished) {
                         // 动画执行完毕后的调用

                     }];
}
```

![](http://upload-images.jianshu.io/upload_images/2648731-ce3c1fe80f7d6734.gif?imageMogr2/auto-orient/strip)

---

### 示例：旋转动画

```objective-c
[UIView animateWithDuration:0.25 animations:^{
   cell.arrow.transform = CGAffineTransformMakeRotation(M_PI);
} completion:^(BOOL finished) {

}];
```







### 示例：嵌套动画

> 摘自[YYText Demo](https://github.com/ibireme/YYText)

```objective-c
- (void)showMessage:(NSString *)msg {
    CGFloat padding = 10;
    
    YYLabel *label = [YYLabel new];
    label.text = msg;
    label.font = [UIFont systemFontOfSize:16];
    label.textAlignment = NSTextAlignmentCenter;
    label.textColor = [UIColor whiteColor];
    label.backgroundColor = [UIColor colorWithRed:0.033 green:0.685 blue:0.978 alpha:0.730];
    label.textContainerInset = UIEdgeInsetsMake(padding, padding, padding, padding);
    label.width = self.view.width;
    label.height = [msg heightForFont:label.font width:label.width] + 2 * padding;
    label.bottom = (kiOS7Later ? 64 : 0);
    [self.view addSubview:label];
    
    // 显示label动画
    [UIView animateWithDuration:0.3 animations:^{
        label.top = (kiOS7Later ? 64 : 0);
    } completion:^(BOOL finished) {
        
        // 嵌套动画，2秒后隐藏label动画
        [UIView animateWithDuration:0.2 delay:2 options:UIViewAnimationOptionCurveEaseInOut animations:^{
            label.bottom = (kiOS7Later ? 64 : 0);
        } completion:^(BOOL finished) {
            [label removeFromSuperview];
        }];
    }];
}
```

![](http://upload-images.jianshu.io/upload_images/2648731-9f387b6bcdd352a2.gif?imageMogr2/auto-orient/strip)

---





## Spring 动画

```objective-c
/* Performs `animations` using a timing curve described by the motion of a spring. When `dampingRatio` is 1, the animation will smoothly decelerate to its final model values without oscillating. Damping ratios less than 1 will oscillate more and more before coming to a complete stop. You can use the initial spring velocity to specify how fast the object at the end of the simulated spring was moving before it was attached. It's a unit coordinate system, where 1 is defined as travelling the total animation distance in a second. So if you're changing an object's position by 200pt in this animation, and you want the animation to behave as if the object was moving at 100pt/s before the animation started, you'd pass 0.5. You'll typically want to pass 0 for the velocity. 

大意：使用弹簧运动描述的时序曲线执行动画。当 dampingRatio (阻尼比)为1时，动画将平稳减速到其最终模型没有振荡。小于1的阻尼比在达到完全停止之前会越来越波动。你可以使用初始的弹簧速度来指定模拟弹簧在到达终点之前移动的速度有多快。velocity（动画速率）是一个单位坐标系，其中1定义为在一秒钟内移动总动画距离。所以在动画执行过程中,如果你想改变对象的移动距离为200pt，并且你想要动画表现得像物体一样 以100pt / s 的速度运动，那么你应该设置弹簧的速率为0.5。你通常想通过0的速度。*/ 
+ (void)animateWithDuration:(NSTimeInterval)duration delay:(NSTimeInterval)delay usingSpringWithDamping:(CGFloat)dampingRatio initialSpringVelocity:(CGFloat)velocity options:(UIViewAnimationOptions)options animations:(void (^)(void))animations completion:(void (^ __nullable)(BOOL finished))completion NS_AVAILABLE_IOS(7_0);
```

### 两个额外参数：

- `dampingRatio`：弹簧动画中的阻尼比。相当于弹簧的硬度，数值在 0.0 到 1.0 之间，数值越小，弹簧越软，波动幅度越大；反之相反。
- `velocity`：弹簧的速率。数值越小，动力越小，弹簧的拉伸幅度就越小。反之相反。比如：总共的动画运行距离是200 points，你希望每秒 100pt ，值为 0.5；




### 示例

```
- (void)viewDidLoad {
    [super viewDidLoad];
    
    UIView *view1 = [[UIView alloc] initWithFrame:CGRectMake(self.view.frame.size.width / 2 - 50, 22, 100, 100)];
    view1.backgroundColor = [UIColor flatMintColor];
    [self.view addSubview:view1];
    
    [UIView animateWithDuration:3.0
                          delay:1.0
         usingSpringWithDamping:0.4    // 弹簧阻尼比
          initialSpringVelocity:0.3    // 速度
                        options:UIViewAnimationOptionLayoutSubviews
                     animations:^{
                         view1.center = self.view.center;
                     }
                     completion:^(BOOL finished) {
                         
                     }];
}
```

![](http://upload-images.jianshu.io/upload_images/2648731-bc5482327e63cd42.gif?imageMogr2/auto-orient/strip)



---



## Transitions 转场动画

常用于添加、删除子视图或隐藏、显示子视图时的动画。

```objective-c
/** 为指定的容器视图创建转换动画 */  
+ (void)transitionWithView:(UIView *)view duration:(NSTimeInterval)duration options:(UIViewAnimationOptions)options animations:(void (^ __nullable)(void))animations completion:(void (^ __nullable)(BOOL finished))completion NS_AVAILABLE_IOS(4_0);  
  
/** 使用给定的参数在指定视图之间创建转换动画 */  
+ (void)transitionFromView:(UIView *)fromView toView:(UIView *)toView duration:(NSTimeInterval)duration options:(UIViewAnimationOptions)options completion:(void (^ __nullable)(BOOL finished))completion NS_AVAILABLE_IOS(4_0); // toView added to fromView.superview, fromView removed from its superview
```



### 示例一：

```objective-c
#pragma mark - Lifecycle

- (void)viewDidLoad {
    [super viewDidLoad];
    [self.view addSubview:self.containerView];
}

- (void)viewDidAppear:(BOOL)animated {
    [super viewDidAppear:animated];
    [UIView transitionWithView:self.containerView
                      duration:1.0
                       options:UIViewAnimationOptionTransitionFlipFromLeft |
     UIViewAnimationOptionRepeat |
     UIViewAnimationOptionAutoreverse
                    animations:^{
                        [self.containerView addSubview:self.subView];
                    }
                    completion:^(BOOL finished) {
                        
                    }];
}

#pragma mark - Custom Accessors

- (UIView *)containerView {
    if (!_containerView) {
        _containerView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 250, 250)];
        _containerView.center = self.view.center;
    }
    return _containerView;
}

- (UIView *)subView {
    if (!_subView) {
        _subView = [[UIView alloc] initWithFrame:self.containerView.bounds];
        _subView.backgroundColor = [UIColor flatGreenColor];
    }
    return _subView;
}
```

![](http://upload-images.jianshu.io/upload_images/2648731-84204eae0af7e78e.gif?imageMogr2/auto-orient/strip)



---

### 示例二：

```objective-c
#pragma mark - Lifecycle

- (void)viewDidLoad {
    [super viewDidLoad];
    [self.view addSubview:self.view1];
    [self.view addSubview:self.button];
}

#pragma mark - Custom Accessors

- (UIView *)view1 {
    if (!_view1) {
        _view1 = [[UIView alloc] initWithFrame:CGRectMake(0, [UIScreen mainScreen].bounds.size.height - 450, [UIScreen mainScreen].bounds.size.width, 450)];
        _view1.backgroundColor = [UIColor flatWatermelonColor];
    }
    return _view1;
}

- (UIView *)view2 {
    if (!_view2) {
        _view2 = [[UIView alloc] initWithFrame:CGRectMake(0, [UIScreen mainScreen].bounds.size.height - 450, [UIScreen mainScreen].bounds.size.width, 450)];
        _view2.backgroundColor = [UIColor flatGreenColor];
    }
    return _view2;
}

#pragma mark - IBActions

- (void)startAnimation:(id)sender {    
    // 转场动画
    [UIView transitionFromView:self.view1
                        toView:self.view2
                      duration:0.25
                       options:UIViewAnimationOptionTransitionFlipFromRight
                    completion:^(BOOL finished) {
                        
                    }];
}
```

![](http://upload-images.jianshu.io/upload_images/2648731-b46ed3961dc56d2a.gif?imageMogr2/auto-orient/strip)



另外，关于 **UINavigationController** 的 push、pop 动画：

[天天品尝iOS7甜点 :: Day 10 :: Custom UIViewController Transitions](http://www.jianshu.com/p/75d7b0341bc5)

---



## Keyframe 动画

```objective-c
/** UIView 的关键帧动画 */
@interface UIView (UIViewKeyframeAnimations)

/** 创建一个动画块对象，可用于为当前视图设置基于关键帧的动画 */ 
+ (void)animateKeyframesWithDuration:(NSTimeInterval)duration delay:(NSTimeInterval)delay options:(UIViewKeyframeAnimationOptions)options animations:(void (^)(void))animations completion:(void (^ __nullable)(BOOL finished))completion NS_AVAILABLE_IOS(7_0);

/** 添加指定开始时间、持续时间的关键帧动画(起始和持续时间是0.0和1.0之间的值) */ 
+ (void)addKeyframeWithRelativeStartTime:(double)frameStartTime relativeDuration:(double)frameDuration animations:(void (^)(void))animations NS_AVAILABLE_IOS(7_0); // start time and duration are values between 0.0 and 1.0 specifying time and duration relative to the overall time of the keyframe animation
// 开始时间和持续时间是相对于关键帧动画的总时间的位于0和1之间的值 

@end
```



### 示例一：

```objective-c
#pragma mark - Lifecycle

- (void)viewDidLoad {
    [super viewDidLoad];
    [self.view addSubview:self.view1];
    
    [UIView animateKeyframesWithDuration:4.0
                                   delay:1.0
                                 options:UIViewKeyframeAnimationOptionRepeat
                              animations:^{
                                  
      [UIView addKeyframeWithRelativeStartTime:0.0
                              relativeDuration:0.25
                                    animations:^{
                                        self.view1.frame = CGRectMake(50, 250, 100, 100);
                                    }];
      [UIView addKeyframeWithRelativeStartTime:0.25
                              relativeDuration:0.25
                                    animations:^{
                                        self.view1.frame = CGRectMake(250, 250, 100, 100);
                                    }];
      [UIView addKeyframeWithRelativeStartTime:0.5
                              relativeDuration:0.25
                                    animations:^{
                                        self.view1.frame = CGRectMake(250, 50, 100, 100);
                                    }];
      [UIView addKeyframeWithRelativeStartTime:0.75
                              relativeDuration:0.25
                                    animations:^{
                                        self.view1.frame = CGRectMake(50, 50, 100, 100);
                                    }];
                              }
                              completion:^(BOOL finished) {
        
    }];
}

#pragma mark - Custom Accessors

- (UIView *)view1 {
    if (!_view1) {
        _view1 = [[UIView alloc] initWithFrame:CGRectMake(50, 50, 100, 100)];
        _view1.backgroundColor = [UIColor flatWatermelonColor];
    }
    return _view1;
}
```

![](http://upload-images.jianshu.io/upload_images/2648731-05101b9ec529665a.gif?imageMogr2/auto-orient/strip)

关键帧动画的其他示例：

[天天品尝iOS7甜点 :: Day 11 :: UIView Key-frame Animations](http://www.jianshu.com/p/a60f029e5c72)



---



## Layer 动画



### 示例一：CABasicAnimation

```objective-c
CABasicAnimation *basicAnimation = [CABasicAnimation animationWithKeyPath:@"position.x"];
basicAnimation.fromValue = @10;
basicAnimation.toValue = @200;
basicAnimation.duration = 2.0;
// 在动画执行过程中保留最后一帧
basicAnimation.fillMode = kCAFillModeForwards;
basicAnimation.removedOnCompletion = NO;
[self.view1.layer addAnimation:basicAnimation forKey:nil];
```

![](http://upload-images.jianshu.io/upload_images/2648731-662500c0ca03318a.gif?imageMogr2/auto-orient/strip)



### 示例二：CAKeyframeAnimation

**UILabel** 的跑马灯效果：

> 摘自：[Wonderful](https://github.com/dsxNiubility/Wonderful)

```objective-c
- (void)moveAction {
    // 以 Label 的中心点运动
    // 贝塞尔曲线 起点
    CGPoint fromPoint = CGPointMake(self.frame.size.width + self.marqueeLbl.frame.size.width/2, self.frame.size.height/2);
    // 贝塞尔曲线 终点
    CGPoint toPoint = CGPointMake(-self.marqueeLbl.frame.size.width/2, self.frame.size.height/2);
    
    self.marqueeLbl.center = fromPoint;
    UIBezierPath *movePath = [UIBezierPath bezierPath];
    [movePath moveToPoint:fromPoint];
    [movePath addLineToPoint:toPoint];
    
    // 创建动画实例
    CAKeyframeAnimation *moveAnim = [CAKeyframeAnimation animationWithKeyPath:@"position"];
    // 设置动画路径
    moveAnim.path = movePath.CGPath;
    // 设置动画时长
    moveAnim.duration = self.marqueeLbl.frame.size.width * self.speedLevel * 0.01;
    // 委托协议，动画完成后重复该动画
    moveAnim.delegate = self;
    // 在图层上添加动画
    [self.marqueeLbl.layer addAnimation:moveAnim forKey:nil];
}

#pragma mark - CAAnimationDelegate

- (void)animationDidStop:(CAAnimation *)anim finished:(BOOL)flag{
    if (flag) {
        [self moveAction];
    }
}
```







## UIView.h 源码解析注释

> 摘自：[ios开发 之 UIView详解](http://blog.csdn.net/zeng_zhiming/article/details/73469182)，部分修改。

```objective-c
//  UIView.h
//  UIKit
//
//  Copyright (c) 2005-2016 Apple Inc. All rights reserved.
//  Created by ZengZhiming on 2017/5/22.  
//

#import <Foundation/Foundation.h>  
#import <QuartzCore/QuartzCore.h>  
#import <UIKit/UIResponder.h>  
#import <UIKit/UIInterface.h>  
#import <UIKit/UIKitDefines.h>  
#import <UIKit/UIAppearance.h>  
#import <UIKit/UIDynamicBehavior.h>  
#import <UIKit/NSLayoutConstraint.h>  
#import <UIKit/UITraitCollection.h>  
#import <UIKit/UIFocus.h>  
  
NS_ASSUME_NONNULL_BEGIN  
  
/** 动画的曲线枚举 */  
typedef NS_ENUM(NSInteger, UIViewAnimationCurve) {  
    UIViewAnimationCurveEaseInOut,  //!< 慢进慢出（默认值）.  
    UIViewAnimationCurveEaseIn,     //!< 慢进.  
    UIViewAnimationCurveEaseOut,    //!< 慢出.  
    UIViewAnimationCurveLinear,     //!< 匀速.  
};  
  
/** UIView 内容填充模式 */
typedef NS_ENUM(NSInteger, UIViewContentMode) {  
    UIViewContentModeScaleToFill,       //!< 缩放内容到合适比例大小.  
    UIViewContentModeScaleAspectFit,    //!< 缩放内容到合适的大小，边界多余部分透明.  
    UIViewContentModeScaleAspectFill,   //!< 缩放内容填充到指定大小，边界多余的部分省略.  
    UIViewContentModeRedraw,            //!< 重绘视图边界 (需调用 -setNeedsDisplay).  
    UIViewContentModeCenter,            //!< 视图保持等比缩放.  
    UIViewContentModeTop,               //!< 视图顶部对齐.  
    UIViewContentModeBottom,            //!< 视图底部对齐.  
    UIViewContentModeLeft,              //!< 视图左侧对齐.  
    UIViewContentModeRight,             //!< 视图右侧对齐.  
    UIViewContentModeTopLeft,           //!< 视图左上角对齐.  
    UIViewContentModeTopRight,          //!< 视图右上角对齐.  
    UIViewContentModeBottomLeft,        //!< 视图左下角对齐.  
    UIViewContentModeBottomRight,       //!< 视图右下角对齐.  
};  
  
/** UIView 动画过渡效果 */  
typedef NS_ENUM(NSInteger, UIViewAnimationTransition) {  
    UIViewAnimationTransitionNone,          //!< 无效果.  
    UIViewAnimationTransitionFlipFromLeft,  //!< 沿视图垂直中心轴左到右移动.  
    UIViewAnimationTransitionFlipFromRight, //!< 沿视图垂直中心轴右到左移动.  
    UIViewAnimationTransitionCurlUp,        //!< 由底部向上卷起.  
    UIViewAnimationTransitionCurlDown,      //!< 由顶部向下展开.  
};  
  
/** 自动调整大小方式 */  
typedef NS_OPTIONS(NSUInteger, UIViewAutoresizing) {  
    UIViewAutoresizingNone                 = 0,     //!< 不自动调整.  
    UIViewAutoresizingFlexibleLeftMargin   = 1 << 0,//!< 自动调整与superView左边的距离，保证与superView右边的距离不变.  
    UIViewAutoresizingFlexibleWidth        = 1 << 1,//!< 自动调整自己的宽度，保证与superView左边和右边的距离不变.  
    UIViewAutoresizingFlexibleRightMargin  = 1 << 2,//!< 自动调整与superView的右边距离，保证与superView左边的距离不变.  
    UIViewAutoresizingFlexibleTopMargin    = 1 << 3,//!< 自动调整与superView顶部的距离，保证与superView底部的距离不变.  
    UIViewAutoresizingFlexibleHeight       = 1 << 4,//!< 自动调整自己的高度，保证与superView顶部和底部的距离不变.  
    UIViewAutoresizingFlexibleBottomMargin = 1 << 5 //!< 自动调整与superView底部的距离，也就是说，与superView顶部的距离不变.  
};  
  
/** UIView动画选项 */  
typedef NS_OPTIONS(NSUInteger, UIViewAnimationOptions) {
    
    // 📌----------------------------------------------------------------------------
    // 1.常规动画属性设置（可以同时选择多个进行设置）
    UIViewAnimationOptionLayoutSubviews            = 1 <<  0, // 动画过程中保证子视图跟随运动。**提交动画的时候布局子控件，表示子控件将和父控件一同动画。**
    UIViewAnimationOptionAllowUserInteraction      = 1 <<  1, // 动画过程中允许用户交互。
    UIViewAnimationOptionBeginFromCurrentState     = 1 <<  2, // 所有视图从当前状态开始运行,而不是从初始状态开始运行。
    UIViewAnimationOptionRepeat                    = 1 <<  3, // 无限重复动画
    UIViewAnimationOptionAutoreverse               = 1 <<  4, // 动画运行到结束点后仍然以动画方式回到初始点。**来回运行动画,前提是设置动画无限重复**
    UIViewAnimationOptionOverrideInheritedDuration = 1 <<  5, // 忽略嵌套动画时间设置。**忽略外层动画嵌套的时间变化曲线**
    UIViewAnimationOptionOverrideInheritedCurve    = 1 <<  6, // 忽略嵌套动画速度设置。**通过改变属性和重绘实现动画效果，如果key没有提交动画将使用快照**
    UIViewAnimationOptionAllowAnimatedContent      = 1 <<  7, // 动画过程中重绘视图（注意仅仅适用于转场动画）。
    UIViewAnimationOptionShowHideTransitionViews   = 1 <<  8, // 视图切换时直接隐藏旧视图、显示新视图，而不是将旧视图从父视图移除（仅仅适用于转场动画）**用显隐的方式替代添加移除图层的动画效果**
    UIViewAnimationOptionOverrideInheritedOptions  = 1 <<  9, // 不继承父动画设置或动画类型。**忽略嵌套继承的选项**
    
    // 📌----------------------------------------------------------------------------
    // 2.动画速度控制（可从其中选择一个设置）时间函数曲线相关**时间曲线函数**
    UIViewAnimationOptionCurveEaseInOut            = 0 << 16, // 默认，动画先缓慢，然后逐渐加速。
    UIViewAnimationOptionCurveEaseIn               = 1 << 16, // 动画逐渐变慢。
    UIViewAnimationOptionCurveEaseOut              = 2 << 16, // 动画逐渐加速。
    UIViewAnimationOptionCurveLinear               = 3 << 16, // 动画匀速执行。
    
    // 📌-----------------------------------------------------------------------------
    // 3.转场类型（仅适用于转场动画设置，可以从中选择一个进行设置，基本动画、关键帧动画不需要设置）**转场动画相关**
    UIViewAnimationOptionTransitionNone            = 0 << 20, // 默认，没有转场动画效果。
    UIViewAnimationOptionTransitionFlipFromLeft    = 1 << 20, // 从左侧翻转效果。
    UIViewAnimationOptionTransitionFlipFromRight   = 2 << 20, // 从右侧翻转效果。
    UIViewAnimationOptionTransitionCurlUp          = 3 << 20, // 向后翻页的动画过渡效果。
    UIViewAnimationOptionTransitionCurlDown        = 4 << 20, // 向前翻页的动画过渡效果。
    UIViewAnimationOptionTransitionCrossDissolve   = 5 << 20, // 旧视图溶解消失显示下一个新视图的效果。
    UIViewAnimationOptionTransitionFlipFromTop     = 6 << 20, // 从上方翻转效果。
    UIViewAnimationOptionTransitionFlipFromBottom  = 7 << 20, // 从底部翻转效果。
    
    // 📌-----------------------------------------------------------------------------
    // 4.设置帧速率
    UIViewAnimationOptionPreferredFramesPerSecondDefault     = 0 << 24, // 默认的帧每秒.
    UIViewAnimationOptionPreferredFramesPerSecond60          = 3 << 24, // 60帧每秒的帧速率.
    UIViewAnimationOptionPreferredFramesPerSecond30          = 7 << 24, // 30帧每秒的帧速率.
    
    /*
      补充：关于转场动画,它一般是用在这个方法中的:
     
    　[UIView transitionFromView: toView: duration: options:  completion:^(****BOOL****finished) {}];
     该方法效果是插入一面视图移除一面视图，期间可以使用一些转场动画效果。
     */
} NS_ENUM_AVAILABLE_IOS(4_0);

/** UIView 关键帧动画 */ 
typedef NS_OPTIONS(NSUInteger, UIViewKeyframeAnimationOptions) {  
    UIViewKeyframeAnimationOptionLayoutSubviews            = UIViewAnimationOptionLayoutSubviews, //!< 动画过程中保证子视图跟随运动.  
    UIViewKeyframeAnimationOptionAllowUserInteraction      = UIViewAnimationOptionAllowUserInteraction, //!< 动画过程中允许用户交互.  
    UIViewKeyframeAnimationOptionBeginFromCurrentState     = UIViewAnimationOptionBeginFromCurrentState, //!< 所有视图从当前状态开始运行.  
    UIViewKeyframeAnimationOptionRepeat                    = UIViewAnimationOptionRepeat, //!< 重复运行动画.  
    UIViewKeyframeAnimationOptionAutoreverse               = UIViewAnimationOptionAutoreverse, //!< 动画运行到结束点后仍然以动画方式回到初始点.  
    UIViewKeyframeAnimationOptionOverrideInheritedDuration = UIViewAnimationOptionOverrideInheritedDuration, //!< 忽略嵌套动画时间设置.  
    UIViewKeyframeAnimationOptionOverrideInheritedOptions  = UIViewAnimationOptionOverrideInheritedOptions, //!< 不继承父动画设置或动画类型.  
      
    UIViewKeyframeAnimationOptionCalculationModeLinear     = 0 << 10, //!< 连续运算模式, 默认.  
    UIViewKeyframeAnimationOptionCalculationModeDiscrete   = 1 << 10, //!< 离散运算模式.  
    UIViewKeyframeAnimationOptionCalculationModePaced      = 2 << 10, //!< 均匀执行运算模式.  
    UIViewKeyframeAnimationOptionCalculationModeCubic      = 3 << 10, //!< 平滑运算模式.  
    UIViewKeyframeAnimationOptionCalculationModeCubicPaced = 4 << 10  //!< 平滑均匀运算模式.  
} NS_ENUM_AVAILABLE_IOS(7_0);  
  
typedef NS_ENUM(NSUInteger, UISystemAnimation) {  
    UISystemAnimationDelete,    //!< 系统删除动画  
} NS_ENUM_AVAILABLE_IOS(7_0);  
  
typedef NS_ENUM(NSInteger, UIViewTintAdjustmentMode) {  
    UIViewTintAdjustmentModeAutomatic,  //!< 自动的，与父视图相同.  
      
    UIViewTintAdjustmentModeNormal,     //!< 未经修改的.  
    UIViewTintAdjustmentModeDimmed,     //!< 饱和、暗淡的原始色.  
} NS_ENUM_AVAILABLE_IOS(7_0);  
  
typedef NS_ENUM(NSInteger, UISemanticContentAttribute) {  
    UISemanticContentAttributeUnspecified = 0,  //!< 未指定，默认值  
    UISemanticContentAttributePlayback,         //!< 打开/ RW / FF等播放控制按钮  
    UISemanticContentAttributeSpatial,          //!< 控制导致某种形式的定向改变UI中,如分段控制文本对齐方式或在游戏中方向键  
    UISemanticContentAttributeForceLeftToRight, //!< 视图总是从左向右布局.  
    UISemanticContentAttributeForceRightToLeft  //!< 视图总是从右向左布局.  
} NS_ENUM_AVAILABLE_IOS(9_0);  
  
@protocol UICoordinateSpace <NSObject>  
  
/** 将像素point由point所在视图转换到目标视图view中，返回在目标视图view中的像素值 */  
- (CGPoint)convertPoint:(CGPoint)point toCoordinateSpace:(id <UICoordinateSpace>)coordinateSpace NS_AVAILABLE_IOS(8_0);  
/** 将像素point由point所在视图转换到目标视图view中，返回在目标视图view中的像素值 */  
- (CGPoint)convertPoint:(CGPoint)point fromCoordinateSpace:(id <UICoordinateSpace>)coordinateSpace NS_AVAILABLE_IOS(8_0);  
/** 将rect由rect所在视图转换到目标视图view中，返回在目标视图view中的rect */  
- (CGRect)convertRect:(CGRect)rect toCoordinateSpace:(id <UICoordinateSpace>)coordinateSpace NS_AVAILABLE_IOS(8_0);  
/** 将rect从view中转换到当前视图中，返回在当前视图中的rect */  
- (CGRect)convertRect:(CGRect)rect fromCoordinateSpace:(id <UICoordinateSpace>)coordinateSpace NS_AVAILABLE_IOS(8_0);  
  
/** 获取bounds */  
@property (readonly, nonatomic) CGRect bounds NS_AVAILABLE_IOS(8_0);  
  
@end  
  
@class UIBezierPath, UIEvent, UIWindow, UIViewController, UIColor, UIGestureRecognizer, UIMotionEffect, CALayer, UILayoutGuide;  

// 📌----------------------------------------------------------------------------
// UIView 
NS_CLASS_AVAILABLE_IOS(2_0) @interface UIView : UIResponder <NSCoding, UIAppearance, UIAppearanceContainer, UIDynamicItem, UITraitEnvironment, UICoordinateSpace, UIFocusItem, CALayerDelegate>  
  
/** 返回主 layer 所使用的类 */  
#if UIKIT_DEFINE_AS_PROPERTIES  
@property(class, nonatomic, readonly) Class layerClass;  
#else  
+ (Class)layerClass;  
#endif  
  
/** 通过设置 Frame 初始化 UIView 对象 */  
- (instancetype)initWithFrame:(CGRect)frame NS_DESIGNATED_INITIALIZER;  
/** 用于xib初始化 */  
- (nullable instancetype)initWithCoder:(NSCoder *)aDecoder NS_DESIGNATED_INITIALIZER;  
  
/** 设置用户交互，默认YES允许用户交互 */  
@property(nonatomic,getter=isUserInteractionEnabled) BOOL userInteractionEnabled;  
/** 控件标记(父控件可以通过tag找到对应的子控件)，默认为0 */  
@property(nonatomic)                                 NSInteger tag;  
/** 视图图层(可以用来设置圆角效果/阴影效果) */  
@property(nonatomic,readonly,strong)                 CALayer  *layer;  
  
/** 返回是否可以成为焦点, 默认NO */  
#if UIKIT_DEFINE_AS_PROPERTIES  
@property(nonatomic,readonly) BOOL canBecomeFocused NS_AVAILABLE_IOS(9_0);  
#else  
- (BOOL)canBecomeFocused NS_AVAILABLE_IOS(9_0);  
#endif  
/** 是否可以被聚焦 */  
@property (readonly, nonatomic, getter=isFocused) BOOL focused NS_AVAILABLE_IOS(9_0);  
  
/** 左右滑动翻转效果 */  
@property (nonatomic) UISemanticContentAttribute semanticContentAttribute NS_AVAILABLE_IOS(9_0);  
  
/** 获取视图的方向 */  
+ (UIUserInterfaceLayoutDirection)userInterfaceLayoutDirectionForSemanticContentAttribute:(UISemanticContentAttribute)attribute NS_AVAILABLE_IOS(9_0);  
  
/** 获取相对于指定视图的界面方向 */  
+ (UIUserInterfaceLayoutDirection)userInterfaceLayoutDirectionForSemanticContentAttribute:(UISemanticContentAttribute)semanticContentAttribute relativeToLayoutDirection:(UIUserInterfaceLayoutDirection)layoutDirection NS_AVAILABLE_IOS(10_0);  
  
/** 返回即时内容的布局的方向 */  
@property (readonly, nonatomic) UIUserInterfaceLayoutDirection effectiveUserInterfaceLayoutDirection NS_AVAILABLE_IOS(10_0);  
  
@end  

// 📌----------------------------------------------------------------------------
// UIView 范畴类，几何 
@interface UIView(UIViewGeometry)  
  
/** 位置和尺寸(以父控件的左上角为坐标原点(0, 0)) */  
@property(nonatomic) CGRect            frame;  
  
/** 位置和尺寸(以自己的左上角为坐标原点(0, 0)) */  
@property(nonatomic) CGRect            bounds;  
/** 中心点(以父控件的左上角为坐标原点(0, 0)) */  
@property(nonatomic) CGPoint           center;  
/** 变形属性(平移\缩放\旋转) */  
@property(nonatomic) CGAffineTransform transform;  
/** 视图内容的缩放比例 */  
@property(nonatomic) CGFloat           contentScaleFactor NS_AVAILABLE_IOS(4_0);  
  
/** 是否支持多点触摸，默认NO */  
@property(nonatomic,getter=isMultipleTouchEnabled) BOOL multipleTouchEnabled __TVOS_PROHIBITED;  
/** 是否独占整个Touch事件，默认NO */  
@property(nonatomic,getter=isExclusiveTouch) BOOL       exclusiveTouch __TVOS_PROHIBITED;  
  
/** 在指定点上点击测试指定事件 */  
- (nullable UIView *)hitTest:(CGPoint)point withEvent:(nullable UIEvent *)event;  
/** 判断当前的点击或者触摸事件的点是否在当前的view中，默认返回YES */  
- (BOOL)pointInside:(CGPoint)point withEvent:(nullable UIEvent *)event;  
  
/** 将像素point由point所在视图转换到目标视图view中，返回在目标视图view中的像素值 */  
- (CGPoint)convertPoint:(CGPoint)point toView:(nullable UIView *)view;  
/** 将像素point由point所在视图转换到目标视图view中，返回在目标视图view中的像素值 */  
- (CGPoint)convertPoint:(CGPoint)point fromView:(nullable UIView *)view;  
/** 将rect由rect所在视图转换到目标视图view中，返回在目标视图view中的rect */  
- (CGRect)convertRect:(CGRect)rect toView:(nullable UIView *)view;  
/** 将rect从view中转换到当前视图中，返回在当前视图中的rect */  
- (CGRect)convertRect:(CGRect)rect fromView:(nullable UIView *)view;  
  
/** 自动调整子视图尺寸，默认YES，如果 self.bounds 改变了，则会根据 autoresizingMask 属性自动调整子视图尺寸 */  
@property(nonatomic) BOOL               autoresizesSubviews;  
/** 自动调整子视图与父视图的位置，默认 UIViewAutoresizingNone */  
@property(nonatomic) UIViewAutoresizing autoresizingMask;  
  
/** 返回“最佳”尺寸以适应给定尺寸。不实际调整视图大小。默认为返回现有视图大小。*/  
- (CGSize)sizeThatFits:(CGSize)size;  
/** 调整为刚好合适子视图大小 */  
- (void)sizeToFit;  
  
@end  

// 📌----------------------------------------------------------------------------
// UIView 范畴类，视图层次结构
@interface UIView(UIViewHierarchy)  
  
/** 获取父视图 */  
@property(nullable, nonatomic,readonly) UIView       *superview;  
/** 获取所有子视图 */  
@property(nonatomic,readonly,copy) NSArray<__kindof UIView *> *subviews;  
/** 获取视图所在的Window */  
@property(nullable, nonatomic,readonly) UIWindow     *window;  
  
/** 从父视图中移除控件 */  
- (void)removeFromSuperview;  
/** 插入子视图(将子视图插入到subviews数组中index这个位置) */  
- (void)insertSubview:(UIView *)view atIndex:(NSInteger)index;  
/** 交换subviews数组中所存放子视图的位置 */  
- (void)exchangeSubviewAtIndex:(NSInteger)index1 withSubviewAtIndex:(NSInteger)index2;  
  
/** 添加子视图(新添加的视图在subviews数组的后面, 显示在最上面) */  
- (void)addSubview:(UIView *)view;  
/** 插入子视图(将子视图插到siblingSubview之下) */  
- (void)insertSubview:(UIView *)view belowSubview:(UIView *)siblingSubview;  
/** 插入子视图(将子视图插到siblingSubview之上) */  
- (void)insertSubview:(UIView *)view aboveSubview:(UIView *)siblingSubview;  
  
/** 将子视图拉到最上面来显示 */  
- (void)bringSubviewToFront:(UIView *)view;  
/** 将子视图拉到最下面来显示 */  
- (void)sendSubviewToBack:(UIView *)view;  
  
#pragma mark - 系统自动调用(留给子类去实现)  
/** 添加子视图完成后调用 */  
- (void)didAddSubview:(UIView *)subview;  
/** 将要移除子视图时调用 */  
- (void)willRemoveSubview:(UIView *)subview;  
  
/** 将要移动到新父视图时调用 */  
- (void)willMoveToSuperview:(nullable UIView *)newSuperview;  
/** 移动到新父视图完成后调用 */  
- (void)didMoveToSuperview;  
/** 将要移动到新 Window 时调用 */  
- (void)willMoveToWindow:(nullable UIWindow *)newWindow;  
/** 移动到新 Window 完成后调用 */  
- (void)didMoveToWindow;  
  
/** 判断view是否为子类 */  
- (BOOL)isDescendantOfView:(UIView *)view;  
/** 通过tag获得对应的子视图 */  
- (nullable __kindof UIView *)viewWithTag:(NSInteger)tag;  
  
/** 对现在有布局有调整更改后，使用这个方法进行更新 */  
- (void)setNeedsLayout;  
/** 强制进行更新layout */  
- (void)layoutIfNeeded;  
  
/** 控件的frame发生改变的时候就会调用,一般在这里重写布局子控件的位置和尺寸 */  
- (void)layoutSubviews;  
  
/** 设置view之间的间距，该属性只对autolayout布局有效 */  
@property (nonatomic) UIEdgeInsets layoutMargins NS_AVAILABLE_IOS(8_0);  
/** 是否将当前视图的间距和父视图相同，默认是NO */  
@property (nonatomic) BOOL preservesSuperviewLayoutMargins NS_AVAILABLE_IOS(8_0);  
/** 改变view的layoutMargins这个属性时，会触发这个方法 */  
- (void)layoutMarginsDidChange NS_AVAILABLE_IOS(8_0);  
  
/** 视图间距引导 */  
@property(readonly,strong) UILayoutGuide *layoutMarginsGuide NS_AVAILABLE_IOS(9_0);  
  
/** 获取此区域的内的布局引导 */  
@property (nonatomic, readonly, strong) UILayoutGuide *readableContentGuide  NS_AVAILABLE_IOS(9_0);  
@end  

// 📌----------------------------------------------------------------------------
// UIView 范畴类，视图渲染
@interface UIView(UIViewRendering)  
  
/** 重写drawRect方法，在可以这里进行绘图操作。*/  
- (void)drawRect:(CGRect)rect;  
  
/** 标记整个视图的边界矩形需要重绘, 调用这个方法会自动调用drawRect方法 */  
- (void)setNeedsDisplay;  
/** 标记在指定区域内的视图的边界需要重绘, 调用这个方法会自动调用drawRect方法 */  
- (void)setNeedsDisplayInRect:(CGRect)rect;  
  
/** 是否裁剪超出Bounds范围的子控件，默认NO */  
@property(nonatomic)                 BOOL              clipsToBounds;  
/** 设置背景颜色，默认nil */  
@property(nullable, nonatomic,copy)  UIColor          *backgroundColor UI_APPEARANCE_SELECTOR;  
/** 设置透明度(范围0.0~1.0)，默认1.0 */  
@property(nonatomic)                 CGFloat           alpha;  
/** 设置是否不透明，默认YES不透明 */  
@property(nonatomic,getter=isOpaque) BOOL              opaque;  
/** 视图重绘前是否先清理以前的内容，默认YES */  
@property(nonatomic)                 BOOL              clearsContextBeforeDrawing;  
/** 设置是否隐藏，默认NO不隐藏 */  
@property(nonatomic,getter=isHidden) BOOL              hidden;  
/** 内容显示的模式，默认UIViewContentModeScaleToFill */  
@property(nonatomic)                 UIViewContentMode contentMode;  
/** 拉伸属性，如图片拉伸 */  
@property(nonatomic)                 CGRect            contentStretch NS_DEPRECATED_IOS(3_0,6_0) __TVOS_PROHIBITED;  
  
/** 蒙板view */  
@property(nullable, nonatomic,strong)          UIView           *maskView NS_AVAILABLE_IOS(8_0);  
  
/** 改变应用程序的外观的颜色。默认为nil */  
@property(null_resettable, nonatomic, strong) UIColor *tintColor NS_AVAILABLE_IOS(7_0);  
  
/** 可以使tintColor变暗，因此整个视图层次变暗 */  
@property(nonatomic) UIViewTintAdjustmentMode tintAdjustmentMode NS_AVAILABLE_IOS(7_0);  
  
/** 覆盖这个方法的目的是为了当tintColor改变的时候自定义一些行为 */  
- (void)tintColorDidChange NS_AVAILABLE_IOS(7_0);  
  
@end  

// 📌----------------------------------------------------------------------------
// UIView 范畴类，视图动画
@interface UIView(UIViewAnimation)  
  
/** 开始动画 */  
+ (void)beginAnimations:(nullable NSString *)animationID context:(nullable voidvoid *)context;  
/** 提交动画 */  
+ (void)commitAnimations;  
  
/** 设置动画代理, 默认nil */  
+ (void)setAnimationDelegate:(nullable id)delegate;  
/** 动画将要开始时执行方法（必须要先设置动画代理）, 默认NULL */  
+ (void)setAnimationWillStartSelector:(nullable SEL)selector;  
/** 动画已结束时执行方法（必须要先设置动画代理）, 默认NULL */  
+ (void)setAnimationDidStopSelector:(nullable SEL)selector;  
/** 设置动画时长, 默认0.2秒 */  
+ (void)setAnimationDuration:(NSTimeInterval)duration;  
/** 动画延迟执行时间, 默认0.0秒 */  
+ (void)setAnimationDelay:(NSTimeInterval)delay;  
/** 设置在动画块内部动画属性改变的开始时间, 默认now ([NSDate date]) */  
+ (void)setAnimationStartDate:(NSDate *)startDate;  
/** 设置动画曲线, 默认UIViewAnimationCurveEaseInOut */  
+ (void)setAnimationCurve:(UIViewAnimationCurve)curve;  
/** 动画的重复播放次数， 默认0 */  
+ (void)setAnimationRepeatCount:(float)repeatCount;  
/** 设置是否自定翻转当前的动画效果, 默认NO */  
+ (void)setAnimationRepeatAutoreverses:(BOOL)repeatAutoreverses;  
/** 设置动画从当前状态开始播放, 默认NO */  
+ (void)setAnimationBeginsFromCurrentState:(BOOL)fromCurrentState;  
  
/** 在动画块中为视图设置过渡动画 */  
+ (void)setAnimationTransition:(UIViewAnimationTransition)transition forView:(UIView *)view cache:(BOOL)cache;  
  
/** 设置是否激活动画 */  
+ (void)setAnimationsEnabled:(BOOL)enabled;  
/** 返回一个布尔值表示动画是否结束 */  
#if UIKIT_DEFINE_AS_PROPERTIES  
@property(class, nonatomic, readonly) BOOL areAnimationsEnabled;  
#else  
+ (BOOL)areAnimationsEnabled;  
#endif  
/** 先检查动画当前是否启用，然后禁止动画，执行block内的方法，最后重新启用动画，而且这个方法不会阻塞基于CoreAnimation的动画 */  
+ (void)performWithoutAnimation:(void (NS_NOESCAPE ^)(void))actionsWithoutAnimation NS_AVAILABLE_IOS(7_0);  
  
/** 当前动画的持续时间 */  
#if UIKIT_DEFINE_AS_PROPERTIES  
@property(class, nonatomic, readonly) NSTimeInterval inheritedAnimationDuration NS_AVAILABLE_IOS(9_0);  
#else  
+ (NSTimeInterval)inheritedAnimationDuration NS_AVAILABLE_IOS(9_0);  
#endif  
  
@end  

// 📌----------------------------------------------------------------------------
// UIView 范畴类，用 Blocks 方式实现视图动画
@interface UIView(UIViewAnimationWithBlocks)  

/** 
  通过指定动画持续时间、动画延迟、执行动画选项和动画完成后回调的 Block 对象,更改一个或多个视图的动画。
*/
+ (void)animateWithDuration:(NSTimeInterval)duration delay:(NSTimeInterval)delay options:(UIViewAnimationOptions)options animations:(void (^)(void))animations completion:(void (^ __nullable)(BOOL finished))completion NS_AVAILABLE_IOS(4_0);  
  
/** 
  通过指定动画持续时间、动画完成后回调的 Block 对象,更改一个或多个视图的动画。
  
  默认：delay = 0.0, options = 0 
*/  
+ (void)animateWithDuration:(NSTimeInterval)duration animations:(void (^)(void))animations completion:(void (^ __nullable)(BOOL finished))completion NS_AVAILABLE_IOS(4_0);  
  
/** 
  通过指定动画持续时间更改一个或多个视图的动画。
  
  默认：delay = 0.0, options = 0, completion = NULL 
*/
+ (void)animateWithDuration:(NSTimeInterval)duration animations:(void (^)(void))animations NS_AVAILABLE_IOS(4_0);  
  
/** 使用与物理弹簧运动相对应的定时曲线执行视图动画 */  
+ (void)animateWithDuration:(NSTimeInterval)duration delay:(NSTimeInterval)delay usingSpringWithDamping:(CGFloat)dampingRatio initialSpringVelocity:(CGFloat)velocity options:(UIViewAnimationOptions)options animations:(void (^)(void))animations completion:(void (^ __nullable)(BOOL finished))completion NS_AVAILABLE_IOS(7_0);  
  
/** 为指定的容器视图创建转换动画 */  
+ (void)transitionWithView:(UIView *)view duration:(NSTimeInterval)duration options:(UIViewAnimationOptions)options animations:(void (^ __nullable)(void))animations completion:(void (^ __nullable)(BOOL finished))completion NS_AVAILABLE_IOS(4_0);  
  
/** 使用给定的参数在指定视图之间创建转换动画 */  
+ (void)transitionFromView:(UIView *)fromView toView:(UIView *)toView duration:(NSTimeInterval)duration options:(UIViewAnimationOptions)options completion:(void (^ __nullable)(BOOL finished))completion NS_AVAILABLE_IOS(4_0); // toView added to fromView.superview, fromView removed from its superview  
  
/** 在一个或多个视图上执行指定的系统提供的动画，以及定义的可选并行动画 */  
+ (void)performSystemAnimation:(UISystemAnimation)animation onViews:(NSArray<__kindof UIView *> *)views options:(UIViewAnimationOptions)options animations:(void (^ __nullable)(void))parallelAnimations completion:(void (^ __nullable)(BOOL finished))completion NS_AVAILABLE_IOS(7_0);  
  
@end  
  
// 📌----------------------------------------------------------------------------
// UIView 范畴类，UIView 的关键帧动画
@interface UIView (UIViewKeyframeAnimations)  
  
/** 创建一个动画块对象，可用于为当前视图设置基于关键帧的动画 */  
+ (void)animateKeyframesWithDuration:(NSTimeInterval)duration delay:(NSTimeInterval)delay options:(UIViewKeyframeAnimationOptions)options animations:(void (^)(void))animations completion:(void (^ __nullable)(BOOL finished))completion NS_AVAILABLE_IOS(7_0);  

/** 添加指定开始时间、持续时间的关键帧动画(起始和持续时间是0.0和1.0之间的值) */  
+ (void)addKeyframeWithRelativeStartTime:(double)frameStartTime relativeDuration:(double)frameDuration animations:(void (^)(void))animations NS_AVAILABLE_IOS(7_0);  
  
@end  

// 📌----------------------------------------------------------------------------
// UIView 范畴类，手势识别
@interface UIView (UIViewGestureRecognizers)  
  
/** 当前视图所附加的所有手势识别器 */  
@property(nullable, nonatomic,copy) NSArray<__kindof UIGestureRecognizer *> *gestureRecognizers NS_AVAILABLE_IOS(3_2);  
  
/** 添加一个手势识别器 */  
- (void)addGestureRecognizer:(UIGestureRecognizer*)gestureRecognizer NS_AVAILABLE_IOS(3_2);  
/** 移除一个手势识别器 */  
- (void)removeGestureRecognizer:(UIGestureRecognizer*)gestureRecognizer NS_AVAILABLE_IOS(3_2);  
  
/** 是否开始一个手势识别器 */  
- (BOOL)gestureRecognizerShouldBegin:(UIGestureRecognizer *)gestureRecognizer NS_AVAILABLE_IOS(6_0);  
  
@end  

// 📌----------------------------------------------------------------------------
// UIView 范畴类，UIView 运动效果
@interface UIView (UIViewMotionEffects)  
  
/** 添加运动效果，当倾斜设备时视图稍微改变其位置 */  
- (void)addMotionEffect:(UIMotionEffect *)effect NS_AVAILABLE_IOS(7_0);  
  
/** 移除运动效果 */  
- (void)removeMotionEffect:(UIMotionEffect *)effect NS_AVAILABLE_IOS(7_0);  
  
/** 所有添加的运动效果 */  
@property (copy, nonatomic) NSArray<__kindof UIMotionEffect *> *motionEffects NS_AVAILABLE_IOS(7_0);  
  
@end  
  
  
typedef NS_ENUM(NSInteger, UILayoutConstraintAxis) {  
    UILayoutConstraintAxisHorizontal = 0,   //!< 水平约束.  
    UILayoutConstraintAxisVertical = 1      //!< 竖直约束.  
};  
  
// 📌----------------------------------------------------------------------------
// UIView 范畴类，UIView 布局约束设置  
@interface UIView (UIConstraintBasedLayoutInstallingConstraints)  
  
/** 获取所有约束 */  
@property(nonatomic,readonly) NSArray<__kindof NSLayoutConstraint *> *constraints NS_AVAILABLE_IOS(6_0);  
  
/** 添加一个约束 */  
- (void)addConstraint:(NSLayoutConstraint *)constraint NS_AVAILABLE_IOS(6_0);  
/** 添加多个约束 */  
- (void)addConstraints:(NSArray<__kindof NSLayoutConstraint *> *)constraints NS_AVAILABLE_IOS(6_0);  
/** 移除一个约束 */  
- (void)removeConstraint:(NSLayoutConstraint *)constraint NS_AVAILABLE_IOS(6_0);  
/** 移除多个约束 */  
- (void)removeConstraints:(NSArray<__kindof NSLayoutConstraint *> *)constraints NS_AVAILABLE_IOS(6_0);  
@end  

// 📌----------------------------------------------------------------------------
// UIView 范畴类，UIView 基于约束的布局核心方法
@interface UIView (UIConstraintBasedLayoutCoreMethods)  
/** 更新视图和其子视图的约束 */  
- (void)updateConstraintsIfNeeded NS_AVAILABLE_IOS(6_0);  
/** 为视图更新约束，可以重写这个方法来设置当前view局部的布局约束 */  
- (void)updateConstraints NS_AVAILABLE_IOS(6_0) NS_REQUIRES_SUPER;  
/** 视图的约束是否需要更新 */  
- (BOOL)needsUpdateConstraints NS_AVAILABLE_IOS(6_0);  
/** 设置视图的约束需要更新，调用这个方法，系统会调用updateConstraints去更新布局 */  
- (void)setNeedsUpdateConstraints NS_AVAILABLE_IOS(6_0);  
@end  

// 📌----------------------------------------------------------------------------
// UIView 范畴类，UIView 约束兼容性设置
@interface UIView (UIConstraintBasedCompatibility)  
  
/** 是否启用自动布局约束，默认YES. IB默认是NO */  
@property(nonatomic) BOOL translatesAutoresizingMaskIntoConstraints NS_AVAILABLE_IOS(6_0);  
  
/** 是否使用约束布局 */  
#if UIKIT_DEFINE_AS_PROPERTIES  
@property(class, nonatomic, readonly) BOOL requiresConstraintBasedLayout NS_AVAILABLE_IOS(6_0);  
#else  
+ (BOOL)requiresConstraintBasedLayout NS_AVAILABLE_IOS(6_0);  
#endif  
  
@end  

// 📌----------------------------------------------------------------------------
// UIView 范畴类，UIView 布局图层约束  
@interface UIView (UIConstraintBasedLayoutLayering)  
  
/** 返回给定框架的视图的对齐矩阵 */  
- (CGRect)alignmentRectForFrame:(CGRect)frame NS_AVAILABLE_IOS(6_0);  
/** 返回给定对齐矩形的视图的frame */  
- (CGRect)frameForAlignmentRect:(CGRect)alignmentRect NS_AVAILABLE_IOS(6_0);  
  
/** 返回从视图的frame上定义的对齐矩阵的边框 */  
#if UIKIT_DEFINE_AS_PROPERTIES  
@property(nonatomic, readonly) UIEdgeInsets alignmentRectInsets NS_AVAILABLE_IOS(6_0);  
#else  
- (UIEdgeInsets)alignmentRectInsets NS_AVAILABLE_IOS(6_0);  
#endif  
  
/** 返回满足基线约束条件的视图 */  
- (UIView *)viewForBaselineLayout NS_DEPRECATED_IOS(6_0, 9_0, "Override -viewForFirstBaselineLayout or -viewForLastBaselineLayout as appropriate, instead") __TVOS_PROHIBITED;  
  
/** 返回用于满足第一基线约束的视图 */  
@property(readonly,strong) UIView *viewForFirstBaselineLayout NS_AVAILABLE_IOS(9_0);  
  
/** 返回用于满足上次基线约束的视图 */  
@property(readonly,strong) UIView *viewForLastBaselineLayout NS_AVAILABLE_IOS(9_0);  
  
  
UIKIT_EXTERN const CGFloat UIViewNoIntrinsicMetric NS_AVAILABLE_IOS(6_0); // -1  
/** 返回接收对象的原本大小 */  
#if UIKIT_DEFINE_AS_PROPERTIES  
@property(nonatomic, readonly) CGSize intrinsicContentSize NS_AVAILABLE_IOS(6_0);  
#else  
- (CGSize)intrinsicContentSize NS_AVAILABLE_IOS(6_0);  
#endif  
/** 废除视图原本内容的size */  
- (void)invalidateIntrinsicContentSize NS_AVAILABLE_IOS(6_0);  
  
/** 设置当视图要变大时，视图的压缩改变方式，返回一个优先权（确定view有多大的优先级阻止自己变大） */  
- (UILayoutPriority)contentHuggingPriorityForAxis:(UILayoutConstraintAxis)axis NS_AVAILABLE_IOS(6_0);  
/** 设置放先权 */  
- (void)setContentHuggingPriority:(UILayoutPriority)priority forAxis:(UILayoutConstraintAxis)axis NS_AVAILABLE_IOS(6_0);  
  
/** 设置当视图要变小时，视图的压缩改变方式，是水平缩小还是垂直缩小，并返回一个优先权（确定有多大的优先级阻止自己变小） */  
- (UILayoutPriority)contentCompressionResistancePriorityForAxis:(UILayoutConstraintAxis)axis NS_AVAILABLE_IOS(6_0);  
/** 设置优先权 */  
- (void)setContentCompressionResistancePriority:(UILayoutPriority)priority forAxis:(UILayoutConstraintAxis)axis NS_AVAILABLE_IOS(6_0);  
@end  
  
// Size To Fit  
  
UIKIT_EXTERN const CGSize UILayoutFittingCompressedSize NS_AVAILABLE_IOS(6_0);  
UIKIT_EXTERN const CGSize UILayoutFittingExpandedSize NS_AVAILABLE_IOS(6_0);  

// 📌----------------------------------------------------------------------------
// UIView 范畴类，UIView 布局拟合尺寸 
@interface UIView (UIConstraintBasedLayoutFittingSize)  
/** 返回满足持有约束的视图的size */  
- (CGSize)systemLayoutSizeFittingSize:(CGSize)targetSize NS_AVAILABLE_IOS(6_0);  
/** 返回满足它所包含的约束的视图的大小 */  
- (CGSize)systemLayoutSizeFittingSize:(CGSize)targetSize withHorizontalFittingPriority:(UILayoutPriority)horizontalFittingPriority verticalFittingPriority:(UILayoutPriority)verticalFittingPriority NS_AVAILABLE_IOS(8_0);  
@end  

// 📌----------------------------------------------------------------------------
// UIView 范畴类，UIView 布局指南支持 
@interface UIView (UILayoutGuideSupport)  
  
/** 此视图拥有布局向导对象的数组 */  
@property(nonatomic,readonly,copy) NSArray<__kindof UILayoutGuide *> *layoutGuides NS_AVAILABLE_IOS(9_0);  
  
/** 向视图中添加布局向导 */  
- (void)addLayoutGuide:(UILayoutGuide *)layoutGuide NS_AVAILABLE_IOS(9_0);  
  
/** 向视图中添加布局向导 */  
- (void)removeLayoutGuide:(UILayoutGuide *)layoutGuide NS_AVAILABLE_IOS(9_0);  
@end  
  
@class NSLayoutXAxisAnchor,NSLayoutYAxisAnchor,NSLayoutDimension;  
@interface UIView (UIViewLayoutConstraintCreation)  
/** 布局视图的前缘框的布局锚点 */  
@property(readonly, strong) NSLayoutXAxisAnchor *leadingAnchor NS_AVAILABLE_IOS(9_0);  
/** 布局视图的后缘边框的布局锚点 */  
@property(readonly, strong) NSLayoutXAxisAnchor *trailingAnchor NS_AVAILABLE_IOS(9_0);  
/** 布局视图的左边框的布局锚点 */  
@property(readonly, strong) NSLayoutXAxisAnchor *leftAnchor NS_AVAILABLE_IOS(9_0);  
/** 布局视图的右边框的布局锚点 */  
@property(readonly, strong) NSLayoutXAxisAnchor *rightAnchor NS_AVAILABLE_IOS(9_0);  
/** 布局视图的顶边框的布局锚点 */  
@property(readonly, strong) NSLayoutYAxisAnchor *topAnchor NS_AVAILABLE_IOS(9_0);  
/** 布局视图的底边框的布局锚点 */  
@property(readonly, strong) NSLayoutYAxisAnchor *bottomAnchor NS_AVAILABLE_IOS(9_0);  
/** 布局视图的宽度 */  
@property(readonly, strong) NSLayoutDimension *widthAnchor NS_AVAILABLE_IOS(9_0);  
/** 布局视图的高度 */  
@property(readonly, strong) NSLayoutDimension *heightAnchor NS_AVAILABLE_IOS(9_0);  
/** 布局视图的水平中心轴 */  
@property(readonly, strong) NSLayoutXAxisAnchor *centerXAnchor NS_AVAILABLE_IOS(9_0);  
/** 布局视图的垂直中心轴 */  
@property(readonly, strong) NSLayoutYAxisAnchor *centerYAnchor NS_AVAILABLE_IOS(9_0);  
/** 一个代表对视图中的文本的最高线基线布置锚 */  
@property(readonly, strong) NSLayoutYAxisAnchor *firstBaselineAnchor NS_AVAILABLE_IOS(9_0);  
/** 一个代表对视图中的文本的最低线基线布置锚 */  
@property(readonly, strong) NSLayoutYAxisAnchor *lastBaselineAnchor NS_AVAILABLE_IOS(9_0);  
  
@end  


// 📌----------------------------------------------------------------------------
// UIView 范畴类，UIView 布局调试   
@interface UIView (UIConstraintBasedLayoutDebugging)  
  
/** 获得实体在不同方向上所有的布局约束 */  
- (NSArray<__kindof NSLayoutConstraint *> *)constraintsAffectingLayoutForAxis:(UILayoutConstraintAxis)axis NS_AVAILABLE_IOS(6_0);  
  
/** 可以知道当前视图的布局是否会有歧义 */  
#if UIKIT_DEFINE_AS_PROPERTIES  
@property(nonatomic, readonly) BOOL hasAmbiguousLayout NS_AVAILABLE_IOS(6_0);  
#else  
- (BOOL)hasAmbiguousLayout NS_AVAILABLE_IOS(6_0);  
#endif  
  
/** 这个方法会随机改变视图的layout到另外一个有效的layout。这样我们就可以很清楚的看到哪一个layout导致了整体的布局约束出现了错误，或者我们应该增加更多的布局约束 */  
- (void)exerciseAmbiguityInLayout NS_AVAILABLE_IOS(6_0);  
@end  

// 📌----------------------------------------------------------------------------
/** 约束调试，只在DEBUG环境下被调用 */  
@interface UILayoutGuide (UIConstraintBasedLayoutDebugging)  
  
/** 获得实体在不同方向上所有的布局约束 */  
- (NSArray<__kindof NSLayoutConstraint *> *)constraintsAffectingLayoutForAxis:(UILayoutConstraintAxis)axis NS_AVAILABLE_IOS(10_0);  
  
/** 可以知道当前视图的布局是否会有歧义 */  
#if UIKIT_DEFINE_AS_PROPERTIES  
@property(nonatomic, readonly) BOOL hasAmbiguousLayout NS_AVAILABLE_IOS(10_0);  
#else  
- (BOOL)hasAmbiguousLayout NS_AVAILABLE_IOS(10_0);  
#endif  
@end  

// 📌----------------------------------------------------------------------------
#pragma mark - View状态保存恢复  
@interface UIView (UIStateRestoration)  
/** 标示是否支持保存,恢复视图状态信息 */  
@property (nullable, nonatomic, copy) NSString *restorationIdentifier NS_AVAILABLE_IOS(6_0);  
/** 保存视图状态相关的信息 */  
- (void) encodeRestorableStateWithCoder:(NSCoder *)coder NS_AVAILABLE_IOS(6_0);  
/** 恢复和保持视图状态相关信息 */  
- (void) decodeRestorableStateWithCoder:(NSCoder *)coder NS_AVAILABLE_IOS(6_0);  
@end  

// 📌----------------------------------------------------------------------------  
#pragma mark - View快照  
@interface UIView (UISnapshotting)  
/** 将当前显示的view截取成一个新的view */  
- (nullable UIView *)snapshotViewAfterScreenUpdates:(BOOL)afterUpdates NS_AVAILABLE_IOS(7_0);  
/** 缩放一个view默认是从中心点进行缩放的 */  
- (nullable UIView *)resizableSnapshotViewFromRect:(CGRect)rect afterScreenUpdates:(BOOL)afterUpdates withCapInsets:(UIEdgeInsets)capInsets NS_AVAILABLE_IOS(7_0);  
/** 屏幕快照 */  
- (BOOL)drawViewHierarchyInRect:(CGRect)rect afterScreenUpdates:(BOOL)afterUpdates NS_AVAILABLE_IOS(7_0);  
@end  
  
NS_ASSUME_NONNULL_END  
```


### 参考

* [iOS Core Animation: Advanced Techniques中文译本](https://zsisme.gitbooks.io/ios-/)
* [iOS动画教程（1）—王土垚的博客](http://blog.wangtuyao.com/post/2015/12/10/ios-animation-tutorial-1.html)
* [iOS动画教程（2）—王土垚的博客](http://blog.wangtuyao.com/post/2015/12/19/ios-animation-tutorial-2.html)
* [ios开发 之 UIView详解](http://blog.csdn.net/zeng_zhiming/article/details/73469182)
* [iOS开发基础知识：Core Animation(核心动画)](http://www.jianshu.com/p/8c1c1697c0ce)
* [内存恶鬼drawRect](http://bihongbo.com/2016/01/03/memoryGhostdrawRect/)
