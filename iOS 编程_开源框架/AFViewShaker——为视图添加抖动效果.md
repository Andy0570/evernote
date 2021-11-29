* GitHub源码:[AFViewShaker](https://github.com/ArtFeel/AFViewShaker)
* star:1100+

>                                 ⭐️⭐️⭐️ 
以下内容来源于官方源码、 README 文档、测试 Demo或个人使用总结 ！

AFViewShaker
=======
[[图片上传失败...(image-a1b1a5-1510132021807)]](http://cocoapods.org)
[[图片上传失败...(image-914a1b-1510132021807)]](https://github.com/Carthage/Carthage)
[[图片上传失败...(image-3b1e4c-1510132021807)]]()

## About
**AFViewShaker** 实现了简单的 **UIView** 砖块抖动效果。

![](http://upload-images.jianshu.io/upload_images/2648731-2c1cdf7f5fc952df.gif?imageMogr2/auto-orient/strip)





## 使用

##### 为一个视图创建抖动效果
```objective-c
AFViewShaker * viewShaker = [[AFViewShaker alloc] initWithView:self.formView];
```

##### 为多个视图创建抖动效果
```objective-c
NSArray * allFields = @[self.emailField, self.passwordField];
AFViewShaker * viewShaker = [[AFViewShaker alloc] initWithViewsArray:allFields];
```

##### 使用默认参数抖动
```objective-c
// 抖动 AFViewShaker 实例中的所有视图
[self.viewShaker shake];

// 抖动指定的视图
[[[AFViewShaker alloc] initWithView:self.allButtons[0]] shake];
```

##### 使用附带参数配置抖动
```objective-c
[self.viewShaker shakeWithDuration:0.6 completion:^{
    NSLog(@"Hello World!");
}];
```



## 源码



##### **AFViewShaker.h**

```objective-c
// 2个初始化方法
- (instancetype)initWithView:(UIView *)view;
- (instancetype)initWithViewsArray:(NSArray *)viewsArray;

// 2个抖动方法
- (void)shake;
- (void)shakeWithDuration:(NSTimeInterval)duration completion:(void (^)())completion;
```



##### AFViewShaker.m

```objective-c
//
//  AFViewShaker
//  AFViewShaker
//
//  Created by Philip Vasilchenko on 03.12.13.
//  Copyright (c) 2014 okolodev. All rights reserved.
//

#import "AFViewShaker.h"

// 默认延时
static NSTimeInterval const kAFViewShakerDefaultDuration = 0.5;
static NSString * const kAFViewShakerAnimationKey = @"kAFViewShakerAnimationKey";

#if __IPHONE_OS_VERSION_MAX_ALLOWED < 100000
// CAAnimationDelegate is not available before iOS 10 SDK
@interface AFViewShaker ()
#else
@interface AFViewShaker () <CAAnimationDelegate>
#endif

/** 包含视图的数组 */
@property (nonatomic, strong) NSArray * views;
/** 完成动画 */
@property (nonatomic, assign) NSUInteger completedAnimations;
/** 完成后调用 Block 对象 */
@property (nonatomic, copy) void (^completionBlock)();

@end


@implementation AFViewShaker

- (instancetype)initWithView:(UIView *)view {
    // 这里只是调用了指定初始化方法，往数组里扔了一个 View
    return [self initWithViewsArray:@[view]];
}

// 指定初始化方法
- (instancetype)initWithViewsArray:(NSArray *)viewsArray {
    self = [super init];
    if (self) {
        self.views = viewsArray;
    }
    return self;
}


#pragma mark - Public methods

- (void)shake {
    // 这里调用了下面的默认指定动画方法，传入的延时是全局静态变量
    [self shakeWithDuration:kAFViewShakerDefaultDuration completion:nil];
}

// 指定动画方法
- (void)shakeWithDuration:(NSTimeInterval)duration completion:(void (^)())completion {
    self.completionBlock = completion;
    for (UIView * view in self.views) {
        // 遍历视图，为每个视图添加动画
        [self addShakeAnimationForView:view withDuration:duration];
    }
}


#pragma mark - Shake Animation

- (void)addShakeAnimationForView:(UIView *)view withDuration:(NSTimeInterval)duration {
    CAKeyframeAnimation * animation = [CAKeyframeAnimation animationWithKeyPath:@"transform.translation.x"];
    CGFloat currentTx = view.transform.tx;
    
    animation.delegate = self;
    animation.duration = duration;
    // 偏移量
    animation.values = @[@(currentTx),
                         @(currentTx + 10),
                         @(currentTx - 8),
                         @(currentTx + 8),
                         @(currentTx - 5),
                         @(currentTx + 5),
                         @(currentTx)];
    // 动画时间
    animation.keyTimes = @[@(0), @(0.225), @(0.425), @(0.6), @(0.75), @(0.875), @(1)];
    animation.timingFunction = [CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseInEaseOut];
    [view.layer addAnimation:animation forKey:kAFViewShakerAnimationKey];
}


#pragma mark - CAAnimation Delegate

- (void)animationDidStop:(CAAnimation *)animation finished:(BOOL)flag {
    self.completedAnimations += 1;
    if (self.completedAnimations >= self.views.count) {
        self.completedAnimations = 0;
        if (self.completionBlock) {
            // 遍历视图，当所有视图的动画完成以后就调用 Block 对象
            self.completionBlock();
        }
    }
}


@end

```

* 配合 MBProgressHUD 使用：
```
//--------------------------------
// .h
/**
 输入文本框抖动效果
 
 @param view 需要抖动的文本框视图
 @param text 抖动结束后显示的 HUD 提示
 @param HUDView 呈现 HUD 的视图
 */
- (void) shakeWithView:(UIView *)view
               HUDText:(NSString *)text
             addToView:(UIView *)HUDView;

//--------------------------------
// .m
- (void) shakeWithView:(UIView *)view
               HUDText:(NSString *)text
             addToView:(UIView *)HUDView {
    AFViewShaker *viewShaker = [[AFViewShaker alloc] initWithView:view];
    [viewShaker shakeWithDuration:KAFViewShakerDuration
                       completion:^{
        [MBProgressHUD hql_showTextHUD:text toView:HUDView];
    }];
}
```
