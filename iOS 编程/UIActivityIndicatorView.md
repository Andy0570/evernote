显示任务正在进行中的活动指示器视图。

## UIActivityIndicatorView.h 源码

```objective-c
#import <UIKit/UIView.h>
#import <UIKit/UIKitDefines.h>

NS_ASSUME_NONNULL_BEGIN

// 活动指示器样式
typedef NS_ENUM(NSInteger, UIActivityIndicatorViewStyle) {
    UIActivityIndicatorViewStyleWhiteLarge, // (37,37)
    UIActivityIndicatorViewStyleWhite,      // (22,22)
    UIActivityIndicatorViewStyleGray __TVOS_PROHIBITED, // (22, 22)
};

NS_CLASS_AVAILABLE_IOS(2_0) @interface UIActivityIndicatorView : UIView <NSCoding>

// 初始化方法
- (instancetype)initWithActivityIndicatorStyle:(UIActivityIndicatorViewStyle)style NS_DESIGNATED_INITIALIZER; // sizes the view according to the style
- (instancetype)initWithFrame:(CGRect)frame NS_DESIGNATED_INITIALIZER;
- (instancetype) initWithCoder:(NSCoder *)coder NS_DESIGNATED_INITIALIZER;
   
@property(nonatomic) UIActivityIndicatorViewStyle activityIndicatorViewStyle; // 活动指示器样式，默认为 UIActivityIndicatorViewStyleWhite
@property(nonatomic) BOOL                         hidesWhenStopped;           // 停止旋转时，是否自动隐藏。默认为 YES.

@property (nullable, readwrite, nonatomic, strong) UIColor *color NS_AVAILABLE_IOS(5_0) UI_APPEARANCE_SELECTOR; // 活动指示器颜色

- (void)startAnimating; // 开始旋转
- (void)stopAnimating;  // 停止旋转
#if UIKIT_DEFINE_AS_PROPERTIES
@property(nonatomic, readonly, getter=isAnimating) BOOL animating; // 获取动画状态
#else
- (BOOL)isAnimating; // 获取动画状态
#endif 

@end

NS_ASSUME_NONNULL_END
```



## 使用方法

```objective-c
// 初始化对象
UIActivityIndicatorView *indicatorView = [[UIActivityIndicatorView alloc] initWithActivityIndicatorStyle:UIActivityIndicatorViewStyleWhite];

// 设置位置
indicatorView.frame = self.view.bounds;

// 设置颜色
indicatorView.color = [UIColor blueColor];

indicatorView.hidesWhenStopped = YES; // 默认为YES，可省略

[self.view addSubview:indicatorView];

// 开始旋转
[indicatorView startAnimating];

// do Task...

// 查询活动指示器是否正在旋转
BOOL isRouting = indicatorView.isAnimating;
NSLog(@"%@",isRouting?@"YES":@"NO");

// 结束旋转
[indicatorView stopAnimating];
```



### 修改活动指示器大小

```objective-c
CGAffineTransform transform = CGAffineTransformMakeScale(.7f, .7f);
indicatorView.transform = transform;
```

——[在UIButton上添加UIActivityIndicator及设置大小](http://www.jianshu.com/p/71f0ceff5226)



### UIButton 上添加 UIActivityIndicatorView

```objective-c
#import "ViewController.h"

#ifdef DEBUG

#define MYString [NSString stringWithFormat:@"%s", __FILE__].lastPathComponent
#define NSLog(...) printf("%s 第%d行: %s\n\n",[MYString UTF8String] ,__LINE__, [[NSString stringWithFormat:__VA_ARGS__] UTF8String]);

#else
#define NSLog(...)
#endif

@interface ViewController ()

@property (weak, nonatomic) IBOutlet UIButton *button;
@property (nonatomic, strong) UIActivityIndicatorView *activityView;
@property (nonatomic, assign) NSTimeInterval timeInterval;
@property (nonatomic, strong) NSTimer *timer;

@end

@implementation ViewController

#pragma mark - Lifecycle

- (void)viewDidLoad {
    [super viewDidLoad];
    
    self.timeInterval = 5.0;
    [self.button setTitle:@"Button" forState:UIControlStateNormal];
    [self.button setTitle:@"" forState:UIControlStateDisabled];
}

#pragma mark - Custom Accessors

// 活动指示器
- (UIActivityIndicatorView *)activityView {
    if (!_activityView) {
        _activityView = [[UIActivityIndicatorView alloc] initWithFrame:self.button.bounds];
    }
    return _activityView;
}

#pragma mark - IBActions

- (IBAction)button:(id)sender {
    self.button.userInteractionEnabled = NO;
    self.button.enabled = NO;

    [self.button addSubview:self.activityView];
    [self.activityView startAnimating];
    [self startNSTimer];
}

#pragma mark - Private

- (void)startNSTimer {
    // 每隔一秒执行一次，重复执行。
    self.timer = [NSTimer scheduledTimerWithTimeInterval:self.timeInterval
                                                  target:self
                                                selector:@selector(onNSTimerTimeout:)
                                                userInfo:nil
                                                 repeats:NO];
}

- (void)onNSTimerTimeout:(id)sender {
    [self.activityView stopAnimating];
    self.button.userInteractionEnabled = YES;
    self.button.enabled = YES;
}
```

![](http://upload-images.jianshu.io/upload_images/2648731-c03da9d56680070b.gif?imageMogr2/auto-orient/strip)


## 第三方框架
### [DGActivityIndicatorView](https://github.com/ninjaprox/DGActivityIndicatorView) ⭐️500+
![image](http://upload-images.jianshu.io/upload_images/2648731-6cdd9d4f6263c5df.gif?imageMogr2/auto-orient/strip)



> The End.
