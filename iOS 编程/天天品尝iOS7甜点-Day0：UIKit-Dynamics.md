**UIKit Dyanmics** 是 **UIKit** 下的一个二维的物理引擎。

### 参考：

- [GitHub 源码：shinobicontrols/iOS7-day-by-day](https://github.com/ShinobiControls/iOS7-day-by-day)
- [天天品尝iOS7甜点 :: Day 0 :: UIKit Dynamics](http://blog.kingiol.com/archives/page/4/)


### 关键的几个类:

#### UIDynamicBehavior 模拟对象的行为

![](http://upload-images.jianshu.io/upload_images/2648731-2e35531f5b99135f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### UIDynamicAnimator 物理引擎

**UIDynamicAnimator** 自己可以根据不同的行为来计算不同对象间的相互影响。

![](http://upload-images.jianshu.io/upload_images/2648731-085e701454c319f3.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



使用方法总结：

1. 用 **UIDynamicBehavior** 的子类对象设置不同物体各自的行为或者它们通用的行为，然后将它们添加到 **UIDynamicBehavior** 实例对象上，
2. 将 **UIDynamicBehavior** 实例对象添加到 **UIDynamicAnimator** 物理引擎上。

### Demo源码：牛顿摆钟
该 Demo 构建一个牛顿摆钟模拟重力实验。
![](http://upload-images.jianshu.io/upload_images/2648731-ebc9eb7b16f37eab.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



#### SCBallBearingView

```objective-c
// **********************************************
#import <UIKit/UIKit.h>

/**
 圆形钟摆球模型
 */
@interface SCBallBearingView : UIView
@end

// **********************************************
#import "SCBallBearingView.h"
@import QuartzCore;

@implementation SCBallBearingView

- (id)initWithFrame:(CGRect)frame
{
    self = [super initWithFrame:frame];
    if (self) {
        // 初始化代码
        self.backgroundColor = [UIColor lightGrayColor];
        self.layer.cornerRadius = MIN(CGRectGetHeight(frame), CGRectGetWidth(frame)) / 2.0;
        self.layer.borderColor = [UIColor grayColor].CGColor;
        self.layer.borderWidth = 2;
    }
    return self;
}

@end
```

#### ❇️ SCNewtonsCradleView

```objective-c
// **********************************************
#import <UIKit/UIKit.h>

@interface SCNewtonsCradleView : UIView
@end

// **********************************************
#import "SCNewtonsCradleView.h"
#import "SCBallBearingView.h"

@implementation SCNewtonsCradleView {
    NSArray *_ballBearings;             // 钟摆球容器
    UIDynamicAnimator *_animator;       // 用于控制行为的物理引擎
    UIPushBehavior *_userDragBehavior;  // 手势的线性力量
}

- (id)initWithFrame:(CGRect)frame
{
    self = [super initWithFrame:frame];
    if (self) {
        // 初始化代码
        [self createBallBearings];
        [self applyDynamicBehaviors];
        
        // 初始化时施加推力让钟摆球摇摆
        _userDragBehavior = [[UIPushBehavior alloc] initWithItems:@[_ballBearings[0]] mode:UIPushBehaviorModeInstantaneous];
        _userDragBehavior.pushDirection = CGVectorMake(-0.5, 0);
        [_animator addBehavior:_userDragBehavior];
    }
    return self;
}

// 1.创建5个钟摆球
- (void)createBallBearings
{
    NSMutableArray *bbArray = [NSMutableArray array];
    NSUInteger numberBalls = 5;
    CGFloat ballSize = CGRectGetWidth(self.bounds) / (3.0 * (numberBalls - 1));
    
    for (NSUInteger i=0; i<numberBalls; i++) {
        // 钟摆球需要设置得足够小，使它们之间静态摆放时仍留有间隙。
        SCBallBearingView *bb = [[SCBallBearingView alloc] initWithFrame:CGRectMake(0, 0, ballSize - 1, ballSize - 1)];
        
        // 把每个钟摆球放到合适的位置
        CGFloat x = CGRectGetWidth(self.bounds) / 3.0 + i * ballSize;
        CGFloat y = CGRectGetHeight(self.bounds) / 2.0;
        bb.center = CGPointMake(x, y);
        
        // 为每个钟摆球添加一个手势操作，这样可以通过手势来摆弄钟摆球模型:
        UIPanGestureRecognizer *gesture = [[UIPanGestureRecognizer alloc] initWithTarget:self action:@selector(handleBallBearingPan:)];
        [bb addGestureRecognizer:gesture];
        
        // 把钟摆球添加进数组中，并添加为当前页面子视图
        [bbArray addObject:bb];
        [self addSubview:bb];
    }
    _ballBearings = [NSArray arrayWithArray:bbArray];
}

#pragma mark - UIDynamics utility methods
// 2.添加动力行为
- (void)applyDynamicBehaviors
{
    // 创建一个复合的行为集合 UIDynamicBehavior (可以包含很多基本行为)
    UIDynamicBehavior *behavior = [[UIDynamicBehavior alloc] init];
    
    // 将每个球轴承安装到其枢轴点
    for(id<UIDynamicItem> ballBearing in _ballBearings)
    {
        // 2.1 为每个钟摆球添加连结物行为
        UIDynamicBehavior *attachmentBehavior = [self createAttachmentBehaviorForBallBearing:ballBearing];
        [behavior addChildBehavior:attachmentBehavior];
    }
    
    // 2.2 添加重力行为
    [behavior addChildBehavior:[self createGravityBehaviorForObjects:_ballBearings]];
    
    // 2.3 添加碰撞行为
    [behavior addChildBehavior:[self createCollisionBehaviorForObjects:_ballBearings]];
    
    // 2.4 指定碰撞的弹性
    // 使用 UIDynamicItemBehavior 来指定碰撞的弹性。
    // dynamic item 行为也可以设置线性和角速度，把它们添加到手势操作上面是十分有用的。
    UIDynamicItemBehavior *itemBehavior = [[UIDynamicItemBehavior alloc] initWithItems:_ballBearings];
    // 设置了其他的属性
    itemBehavior.elasticity = 1.0;    // 弹性
    itemBehavior.allowsRotation = NO; // rotation(旋转),运行旋转时，可以指定角阻力
    itemBehavior.resistance = 2.f;    // resistance(阻尼，空气产生的阻尼)
    [behavior addChildBehavior:itemBehavior];
    
    // 2.5 创建物理引擎动画
    // 创建物理引擎来控制这些行为，所以我们定了一个类的全局变量(iVar):UIDynamicAnimator *_animator;
    // UIDynamicAnimator代表了模拟动态系统的物理引擎，在这里我们创建了它，并且指定了它参照的view(既指定宇宙空间),也添加了我们创建的行为.
    _animator = [[UIDynamicAnimator alloc] initWithReferenceView:self];
    // Add the composite behavior
    [_animator addBehavior:behavior];
}


// 连结物的行为(attachment behavior)需要添加到钟摆球上面，它代表了绳子对它的牵引作用:
- (UIDynamicBehavior *)createAttachmentBehaviorForBallBearing:(id<UIDynamicItem>)ballBearing
{
    // UIAttachmentBehavior 实例伴随一个动态的对象(一个锚点或者另外的对象),它们有具体的属性来控制绳子对它的行为 - 具体就是它的频繁性、阻尼和长度。默认的设置就是一个完全刚性的连接物(没有任何长度伸缩弹性的物体),刚性的连接物正是我们的钟摆球所需要的。
    
    CGPoint anchor = ballBearing.center;
    // 锚点在钟摆球的正上方
    anchor.y -= CGRectGetHeight(self.bounds) / 4.0;
    
    // 在瞄点上方画一个盒子. 这不是行为的一部分，只是为了视觉上很好看
    UIView *blueBox = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 10, 10)];
    blueBox.backgroundColor = [UIColor blueColor];
    blueBox.center = anchor;
    [self addSubview:blueBox];
    
    // 2.1 创建连结物行为
    UIAttachmentBehavior *behavior = [[UIAttachmentBehavior alloc] initWithItem:ballBearing
                                                               attachedToAnchor:anchor];
    return behavior;
}

- (UIDynamicBehavior *)createGravityBehaviorForObjects:(NSArray *)objects
{
    // 2.2 创建重力行为
    // UIGravityBehavior 代表了对象本身和地球之间的重力吸引力，它有一些属性允许你设置矢量的万有引力(就是重力系数magnitude和方向direction),我们这里增加了重力系数，但是保持重力的方向在y轴上面。
    UIGravityBehavior *gravity = [[UIGravityBehavior alloc] initWithItems:objects];
    gravity.magnitude = 10; // 设置重力系数
    return gravity;
}

- (UIDynamicBehavior *)createCollisionBehaviorForObjects:(NSArray *)objects
{
    // 2.3 创建碰撞行为
    // 将碰撞行为作用在模拟系统中的所有对象上面。
    // 碰撞行为还可以用于模型对象达到边界如视图边界,或任意的贝塞尔曲线路径的界限。
    return [[UICollisionBehavior alloc] initWithItems:objects];
}

#pragma mark - UIGestureRecognizer target method
// 实现手势的具体行为
// UIPushBehavior 代表一个简单的线性力量作用于对象上面。当力消失的时候，我们就回收作用在钟摆球上面的力,我们定义了一个类全局变量(iVar) UIPushBehavior *_userDragBehvior,这个变量可是在手势触发开始的时候赋值，需要记得把这个行为添加到dynamics animator中， 我们力的大小与水平位移成正比， 为了让钟摆球摇摆，我们需要在手势结束的时候删除掉push行为。

- (void)handleBallBearingPan:(UIPanGestureRecognizer *)recognizer
{
    // 拖动手势触发时，创建一个拖动力
    if (recognizer.state == UIGestureRecognizerStateBegan) {
        if(_userDragBehavior) {
            [_animator removeBehavior:_userDragBehavior];
        }
        // 将 UIPushBehavior 对象添加到 UIDynamicAnimator 物理引擎上
        _userDragBehavior = [[UIPushBehavior alloc] initWithItems:@[recognizer.view] mode:UIPushBehaviorModeContinuous];
        [_animator addBehavior:_userDragBehavior];
    }
    
    // 设置力的大小,与水平位移成正比
    _userDragBehavior.pushDirection = CGVectorMake([recognizer translationInView:self].x / 10.f, 0);
    
    // 拖动手势结束时，回收作用在钟摆球上面的力
    if (recognizer.state == UIGestureRecognizerStateEnded) {
        [_animator removeBehavior:_userDragBehavior];
        _userDragBehavior = nil;
    }
}
```

将牛顿钟摆球视图添加到主页：

```objective-c
- (void)viewDidLoad
{
    [super viewDidLoad];
	// Do any additional setup after loading the view, typically from a nib.
    _newtonsCradle = [[SCNewtonsCradleView alloc] initWithFrame:self.view.bounds];
    [self.view addSubview:_newtonsCradle];
}
```

### 动画效果

![](http://upload-images.jianshu.io/upload_images/2648731-18a1ff155672b720.gif?imageMogr2/auto-orient/strip)
