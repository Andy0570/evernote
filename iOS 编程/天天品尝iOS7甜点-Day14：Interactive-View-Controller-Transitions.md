> 为模态视图控制器添加交互式视图转换

### 参考

- [GitHub 源码：shinobicontrols/iOS7-day-by-day](https://github.com/ShinobiControls/iOS7-day-by-day)
- [天天品尝iOS7甜点 :: Day 14 :: Interactive View Controller Transitions](http://blog.kingiol.com/2014/01/18/ios7-day-by-day-day14-interactive-view-controller-transitions/)



这篇文章可能会用到之前两篇文章的知识：

* [天天品尝iOS7甜点 :: Day 10 :: Custom UIViewController Transitions](http://blog.kingiol.com/2014/01/12/ios7-day-by-day-day10-custom-uiviewcontroller-transitions/) - 导航视图控制器自定义转场
* [天天品尝iOS7甜点 :: Day 11 :: UIView Key-frame Animations](http://blog.kingiol.com/2014/01/13/ios7-day-by-day-day11-uiview-key-frame-animations/) - 关键帧动画




### Flip Transition Animation - 翻转过渡效果

#### 默认的模态视图翻转效果

![](http://upload-images.jianshu.io/upload_images/2648731-10e898d96c1c956f.gif?imageMogr2/auto-orient/strip)

#### 自定义模态视图翻转效果

![](http://upload-images.jianshu.io/upload_images/2648731-063a3ec6cd579226.gif?imageMogr2/auto-orient/strip)

#### 实现方式：

1. 视图层次结构

   ![](http://upload-images.jianshu.io/upload_images/2648731-1be0ede8a3c71401.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2. 自定义渐变动画类，遵守 `UIViewControllerAnimatedTransitioning` 协议以实现具体的动画效果：

   ```objective-c
   // ********************************************
   // SCFlipAnimation.h
   #import <Foundation/Foundation.h>

   /**
    遵守<UIViewControllerAnimatedTransitioning>协议的自定义渐变动画
    */
   @interface SCFlipAnimation : NSObject <UIViewControllerAnimatedTransitioning>

   /** 确定页面的翻转方向 */
   @property (nonatomic, assign) BOOL dismissal;

   @end

   // ********************************************
   // SCFlipAnimation.m
   #import "SCFlipAnimation.h"

   @implementation SCFlipAnimation

   - (void)animateTransition:(id<UIViewControllerContextTransitioning>)transitionContext
        {
       // 获取各自的视图控制器
       UIViewController *fromVC = [transitionContext viewControllerForKey:UITransitionContextFromViewControllerKey];
       UIViewController *toVC = [transitionContext viewControllerForKey:UITransitionContextToViewControllerKey];
       
       // 获取视图
       UIView *containerView = [transitionContext containerView];
       UIView *fromView = fromVC.view;
       UIView *toView = toVC.view;
       
       // 添加 toView 到容器视图
       [containerView addSubview:toView];
       
       // 设置 frames
       CGRect initialFrame = [transitionContext initialFrameForViewController:fromVC];
       fromView.frame = initialFrame;
       toView.frame = initialFrame;
       
       // 构建3D转换需要的视角
       CATransform3D transform = CATransform3DIdentity;
       transform.m34 = -1/CGRectGetHeight(initialFrame);
       containerView.layer.sublayerTransform = transform;
       
       // 翻转方向
       CGFloat direction = self.dismissal ? -1.0 : 1.0;
       
       // toView 反向翻转 pi/2
       toView.layer.transform = CATransform3DMakeRotation(-direction * M_PI_2, 1, 0, 0);
       
       // 关键帧动画
       [UIView animateKeyframesWithDuration:[self transitionDuration:transitionContext]
                                      delay:0.0
                                    options:0
                                 animations:^{
                                     // First half is rotating in
               [UIView addKeyframeWithRelativeStartTime:0.0
                                       relativeDuration:0.5
                                             animations:^{
           // fromView 正向翻转 pi/2
           fromView.layer.transform = CATransform3DMakeRotation(direction * M_PI_2, 1, 0, 0);
                                             }];
               [UIView addKeyframeWithRelativeStartTime:0.5
                                       relativeDuration:0.5
                                             animations:^{
           // toView 还原
           toView.layer.transform = CATransform3DMakeRotation(0, 1, 0, 0);
                                             }];
                                 } completion:^(BOOL finished) {
                                     [transitionContext completeTransition:![transitionContext transitionWasCancelled]];
                                 }];
   }

   - (NSTimeInterval)transitionDuration:(id<UIViewControllerContextTransitioning>)transitionContext
        {
       return 1.0;
   }

   @end
   ```

3. First View Controller，遵守 `UIViewControllerTransitioningDelegate` 协议

   ```objective-c
   @interface SCViewController () <UIViewControllerTransitioningDelegate> {
       SCFlipAnimation *_flipAnimation;
   }

   @end

   @implementation SCViewController

   #pragma mark - Lifecycle

   - (void)viewDidLoad
   {
       [super viewDidLoad];
   	// Do any additional setup after loading the view, typically from a nib.
       _flipAnimation = [SCFlipAnimation new];
   }

   #pragma mark - 

   - (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender
   {
       if([segue.destinationViewController isKindOfClass:[SCModalViewController class]]) {
           SCModalViewController *vc = (SCModalViewController *)segue.destinationViewController;
       }
   }

   #pragma mark - UIViewControllerTransitioningDelegate

   // 弹出模态视图控制器
   // 返回遵守 UIViewControllerAnimatedTransitioning 协议的实例
   - (id<UIViewControllerAnimatedTransitioning>)animationControllerForPresentedController:(UIViewController *)presented presentingController:(UIViewController *)presenting sourceController:(UIViewController *)source
   {
       _flipAnimation.dismissal = NO;
       return _flipAnimation;
   }

   // 消失模态视图控制器
   // 返回遵守 UIViewControllerAnimatedTransitioning 协议的实例
   - (id<UIViewControllerAnimatedTransitioning>)animationControllerForDismissedController:(UIViewController *)dismissed
   {
       _flipAnimation.dismissal = YES;
       return _flipAnimation;
   }
   ```

4. Model View Controller 实现按钮方法

   ```objective-c
   // 使模态视图控制器消失
   - (IBAction)handleDismissPressed:(id)sender {
       [self dismissViewControllerAnimated:YES completion:NULL];
   }
   ```



### Interactive transitioning - 交互式转换

用手势上滑、下滑操作实现视图转换

![](http://upload-images.jianshu.io/upload_images/2648731-420374adc092ce3e.gif?imageMogr2/auto-orient/strip)



1. 自定义 `UIPercentDrivenInteractiveTransition ` 子类对象，用于手势处理

   ```objective-c
   // ********************************************
   // SCFlipAnimationInteractor.h
   #import <UIKit/UIKit.h>
   #import "SCInteractiveTransitionViewControllerDelegate.h"

   @interface SCFlipAnimationInteractor : UIPercentDrivenInteractiveTransition

   @property (nonatomic, strong, readonly) UIPanGestureRecognizer *gestureRecogniser;
   @property (nonatomic, assign, readonly) BOOL interactionInProgress;

   // 实现 SCInteractiveTransitionViewControllerDelegate 自定义交互式转场协议的任意视图控制器
   @property (nonatomic, weak) UIViewController<SCInteractiveTransitionViewControllerDelegate> *presentingVC;

   @end

   // ********************************************
   // SCFlipAnimationInteractor.m
   #import "SCFlipAnimationInteractor.h"

   @interface SCFlipAnimationInteractor ()

   @property (nonatomic, strong, readwrite) UIPanGestureRecognizer *gestureRecogniser;
   @property (nonatomic, assign, readwrite) BOOL interactionInProgress;

   @end

   @implementation SCFlipAnimationInteractor

   - (instancetype)init
   {
       self = [super init];
       if (self) {
           self.gestureRecogniser = [[UIPanGestureRecognizer alloc] initWithTarget:self action:@selector(handlePan:)];
       }
       return self;
   }

   #pragma mark - Gesture recognition
   // 手势操作的方法
   - (void)handlePan:(UIPanGestureRecognizer *)pgr
   {
       CGPoint translation = [pgr translationInView:pgr.view];
       // 手势百分比，用百分比的方式来激活指定的动画过程。
       CGFloat percentage  = fabs(translation.y / CGRectGetHeight(pgr.view.bounds));
       switch (pgr.state) {
           case UIGestureRecognizerStateBegan: {
               // 手势开始，执行翻转动画
               self.interactionInProgress = YES;
               // 向目标对象传达执行动画的命令
               [self.presentingVC proceedToNextViewController];
               break;
           }

           case UIGestureRecognizerStateChanged: {
               // 手势进行中，更新翻转进度
               [self updateInteractiveTransition:percentage];
               break;
           }
               
           case UIGestureRecognizerStateEnded: {
               // 手势结束，分进度值处理
               if(percentage < 0.5) {
                   // 1.进度小于0.5，取消转场动画
                   [self cancelInteractiveTransition];
               } else {
                   // 2.进度大于0.5，自动完成转场动画
                   [self finishInteractiveTransition];
               }
               self.interactionInProgress = NO;
               break;
           }
               
           case UIGestureRecognizerStateCancelled: {
               // 取消手势
               [self cancelInteractiveTransition];
               self.interactionInProgress = NO;
               break;
           }
               
           default:
               break;
       }
   }

   @end
   ```

2. 自定义交互协议


   ```objective-c
   #import <Foundation/Foundation.h>

   // 1.该协议的作用：
   // 将手势与目标视图相关联，手势开始时，自定义的手势处理类需要向目标视图发送执行视图转场的命令。
   // 2.为什么要将手势与目标视图关联呢？
   // 因为手势是添加到 window 上的，这样就可以响应预期的行为。
   // 3.为什么手势要添加到 window 上而不是视图上？
   // 因为动画发生的时候，视图控制器中的视图将会被移除，因此手势就没有办法响应行为了。
   @protocol SCInteractiveTransitionViewControllerDelegate <NSObject>

   - (void)proceedToNextViewController;

   @end
   ```

   

3. First View Controller

   ```objective-c
   // ********************************************
   // SCViewController.h
   #import <UIKit/UIKit.h>

   @interface SCViewController : UIViewController

   @end

   // ********************************************
   // SCViewController.m
   #import "SCViewController.h"
   #import "SCModalViewController.h"
   #import "SCFlipAnimationInteractor.h"
   #import "SCFlipAnimation.h"
   #import "SCInteractiveTransitionViewControllerDelegate.h"

   @interface SCViewController () <SCInteractiveTransitionViewControllerDelegate, UIViewControllerTransitioningDelegate> {
       SCFlipAnimationInteractor *_animationInteractor;
       SCFlipAnimation *_flipAnimation;
   }

   @end

   @implementation SCViewController

   #pragma mark - Lifecycle

   - (void)viewDidLoad
   {
       [super viewDidLoad];
   	// Do any additional setup after loading the view, typically from a nib.
       _animationInteractor = [SCFlipAnimationInteractor new];
       _flipAnimation = [SCFlipAnimation new];
   }

   - (void)viewDidAppear:(BOOL)animated
   {
       // 添加手势识别器到window对象中。
       // 这是因为动画发生的时候，视图控制器中的视图将会被移除，因此手势就没有办法响应行为了。
       // 把它添加到window上面，将会确保我们期待的行为。
       if (![self.view.window.gestureRecognizers containsObject:_animationInteractor.gestureRecogniser]) {
           [self.view.window addGestureRecognizer:_animationInteractor.gestureRecogniser];
       }
       // 在第一页中，交互的接收者指向 self 自身
       _animationInteractor.presentingVC = self;
   }

   #pragma mark - 

   - (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender
   {
       if([segue.destinationViewController isKindOfClass:[SCModalViewController class]]) {
           // Set the delegate
           SCModalViewController *vc = (SCModalViewController *)segue.destinationViewController;
           vc.transitioningDelegate = self;
           // 模态视图的手势交互器也指向这个类的手势交互器（弱引用）
           // 也就是说，这两个视图的手势交互器指向的是同一个对象
           vc.interactor = _animationInteractor;
       }
   }

   #pragma mark - SCInteractiveTransitionViewControllerDelegate

   - (void)proceedToNextViewController
   {
       [self performSegueWithIdentifier:@"displayModal" sender:self];
   }

   #pragma mark - UIViewControllerTransitioningDelegate

   // 弹出模态视图控制器
   - (id<UIViewControllerAnimatedTransitioning>)animationControllerForPresentedController:(UIViewController *)presented presentingController:(UIViewController *)presenting sourceController:(UIViewController *)source
   {
       _flipAnimation.dismissal = NO;
       return _flipAnimation;
   }

   // 消失模态视图控制器
   - (id<UIViewControllerAnimatedTransitioning>)animationControllerForDismissedController:(UIViewController *)dismissed
   {
       _flipAnimation.dismissal = YES;
       return _flipAnimation;
   }

   // 交互式转场方法1：展示
   // 返回一个实现 UIViewControllerInteractiveTransitioning 协议的对象
   - (id<UIViewControllerInteractiveTransitioning>)interactionControllerForPresentation:(id<UIViewControllerAnimatedTransitioning>)animator
   {
       return _animationInteractor.interactionInProgress ? _animationInteractor : nil;
   }

   // 交互式转场方法2：消失
   // 返回一个实现 UIViewControllerInteractiveTransitioning 协议的对象
   - (id<UIViewControllerInteractiveTransitioning>)interactionControllerForDismissal:(id<UIViewControllerAnimatedTransitioning>)animator
   {
       return _animationInteractor.interactionInProgress ? _animationInteractor : nil;
   }

   @end
   ```

4. Model View Controller

   ```objective-c
   // ********************************************
   // SCModalViewController.h
   #import <UIKit/UIKit.h>
   #import "SCInteractiveTransitionViewControllerDelegate.h"
   #import "SCFlipAnimationInteractor.h"

   @interface SCModalViewController : UIViewController <SCInteractiveTransitionViewControllerDelegate>

   - (IBAction)handleDismissPressed:(id)sender;

   @property (nonatomic, weak) SCFlipAnimationInteractor *interactor;

   @end

   // ********************************************
   // SCModalViewController.m
   #import "SCModalViewController.h"

   @implementation SCModalViewController

   #pragma mark - Lifecycle

   - (void)viewDidAppear:(BOOL)animated
   {
       // 重新设置哪一个视图控制器是交互式转场的接收者
       // 在模态页面中，交互的接收者又重新设置为模态页面自身
       self.interactor.presentingVC = self;
   }

   #pragma mark - IBActions

   // 使模态视图控制器消失
   - (IBAction)handleDismissPressed:(id)sender {
       [self dismissViewControllerAnimated:YES completion:NULL];
   }

   #pragma mark - SCInteractiveTransitionViewControllerDelegate

   - (void)proceedToNextViewController
   {
       [self dismissViewControllerAnimated:YES completion:NULL];
   }

   @end
   ```



The End.
