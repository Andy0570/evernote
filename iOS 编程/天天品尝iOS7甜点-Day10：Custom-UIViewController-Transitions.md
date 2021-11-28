> 自定义视图控制器转场

### 参考

- [GitHub 源码：shinobicontrols/iOS7-day-by-day](https://github.com/ShinobiControls/iOS7-day-by-day)
- [天天品尝iOS7甜点 :: Day 10 :: Custom UIViewController Transitions](http://blog.kingiol.com/2014/01/12/ios7-day-by-day-day10-custom-uiviewcontroller-transitions/)

### 转场效果



默认的 **UINavigationController** 转场 push/pop 是这样的：

![](http://upload-images.jianshu.io/upload_images/2648731-7ae4510fe6bab92b.gif?imageMogr2/auto-orient/strip)

现在我们要实现自定义实现视图控制器转场效果：

![](http://upload-images.jianshu.io/upload_images/2648731-21ea04ffcb2db9c4.gif?imageMogr2/auto-orient/strip)



### 视图层次结构

视图层次结构很简单：一个 UINavigationController 作为窗口的根视图控制器、而首页是 UINavigationController 的根视图控制器、首页可以 push 到第二页：

![](http://upload-images.jianshu.io/upload_images/2648731-26712bd54b654430.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



#### 要想实现自定义视图控制器转场效果，需要实现 `UINavigationControllerDelegate` 协议。

在 **UINavigationController** 实例对象中声明某个类（`HQLNavControllerDelegate`）实现了`UINavigationControllerDelegate` 协议：

```objective-c
#import "HQLNavigationController.h"
#import "HQLNavControllerDelegate.h"

@interface HQLNavigationController () {
    id<UINavigationControllerDelegate> _navDelegate;
}

@end

@implementation HQLNavigationController

- (instancetype)initWithCoder:(NSCoder *)aDecoder {
    self = [super initWithCoder:aDecoder];
    if (self) {
        _navDelegate = [HQLNavControllerDelegate new];
        self.delegate = _navDelegate;
    }
    return self;
}
```



#### 上面代码中把实现转场效果的协议方法封装成了一个类`HQLNavControllerDelegate`：

```objective-c
// ****************************************************
//  HQLNavControllerDelegate.h
#import <Foundation/Foundation.h>
#import <UIKit/UIKit.h>

@interface HQLNavControllerDelegate : NSObject <UINavigationControllerDelegate>

@end


// ****************************************************
//  HQLNavControllerDelegate.m
#import "HQLNavControllerDelegate.h"
#import "HQLFadeTransition.h"

@implementation HQLNavControllerDelegate

- (nullable id <UIViewControllerAnimatedTransitioning>)navigationController:(UINavigationController *)navigationController
                                            animationControllerForOperation:(UINavigationControllerOperation)operation
                                                         fromViewController:(UIViewController *)fromVC
                                                           toViewController:(UIViewController *)toVC{
    return [HQLFadeTransition new];
}

@end
```



#### 而在 `HQLNavControllerDelegate` 类中又把实现自定义转场效果的转场动画封装成了一个类 `HQLFadeTransition`

```objective-c
// ****************************************************
//  HQLFadeTransition.h
#import <Foundation/Foundation.h>
#import <UIKit/UIKit.h>

@interface HQLFadeTransition : NSObject <UIViewControllerAnimatedTransitioning>

@end

  
// ****************************************************
//  HQLFadeTransition.m
#import "HQLFadeTransition.h"

@implementation HQLFadeTransition

// @required 方法：返回动画的时长
- (NSTimeInterval)transitionDuration:(id<UIViewControllerContextTransitioning>)transitionContext {
    return 2.0;
}

// @required 方法：实现视图控制器之间的转场效果
- (void)animateTransition:(id<UIViewControllerContextTransitioning>)transitionContext {
    
    // 1️⃣ viewControllerForKey:获取与转场相关的两个视图控制器
    // UITransitionContextFromViewControllerKey
    UIViewController *fromVC = [transitionContext viewControllerForKey:UITransitionContextFromViewControllerKey];
    // UITransitionContextToViewControllerKey
    UIViewController *toVC = [transitionContext viewControllerForKey:UITransitionContextToViewControllerKey];
    
    // 2️⃣ containerView - 动画发生的容器视图
    UIView *containerView = [transitionContext containerView];
    
    // 3️⃣ 将两个视图控制器的子视图添加到容器视图
    [containerView addSubview:fromVC.view];
    [containerView addSubview:toVC.view];
    // 4️⃣ 将要转换到的那个视图设置为透明
    toVC.view.alpha = 0.0;
    
    // 5️⃣ 执行动画
    [UIView animateWithDuration:[self transitionDuration:transitionContext]
                          delay:0
                        options:0
                     animations:^{
                         // 动画效果
                         toVC.view.alpha = 1.f;
                     }
                     completion:^(BOOL finished) {
                         // 移除旧视图控制器视图
                         [fromVC.view removeFromSuperview];
                         // 告诉 transitionContext 上下文，动画完成啦!
                         [transitionContext completeTransition:YES];
                     }];
    
}

// 转换完成后调用
- (void)animationEnded:(BOOL) transitionCompleted {
    NSLog(@"Transition Completed!");
}

@end
```



> The end.
