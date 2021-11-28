### UIModalPresentationStyle

```objective-c
typedef enum UIModalPresentationStyle : NSInteger {
 UIModalPresentationFullScreen = 0,
 // UIModalPresentationFullScreen 代表弹出VC时，presented VC充满全屏，如果弹出VC的wantsFullScreenLayout 属性设置为YES的，则会填充到状态栏下边，否则不会填充到状态栏之下。
 
 UIModalPresentationPageSheet,
 //	UIModalPresentationPageSheet 代表弹出VC时，presented VC的高度和当前屏幕高度相同，宽度和竖屏模式下屏幕宽度相同，剩余未覆盖区域将会变暗并阻止用户点击，这种弹出模式下，竖屏时跟 UIModalPresentationFullScreen 的效果一样，横屏时候两边则会留下变暗的区域。
 
 UIModalPresentationFormSheet,
 //	UIModalPresentationFormSheet 这种模式下，presented VC的高度和宽度均会小于屏幕尺寸，presented VC居中显示，四周留下变暗区域。
 
 UIModalPresentationCurrentContext,
 // UIModalPresentationCurrentContext 这种模式下，presented VC 的弹出方式和 presenting VC的父VC的方式相同。
 
 UIModalPresentationCustom,
 //	自定义视图展示风格,由一个自定义演示控制器和一个或多个自定义动画对象组成。符合UIViewControllerTransitioningDelegate 协议。使用视图控制器的 transitioningDelegate设定您的自定义转换。
 
 UIModalPresentationOverFullScreen,
 //	如果视图没有被填满,底层视图可以透过
 
 UIModalPresentationOverCurrentContext,
 //	视图全部被透过
 
 UIModalPresentationPopover,
 // 弹窗样式
  
 UIModalPresentationNone = -1
 } UIModalPresentationStyle;
```



###  UIModalTransitionStyle

```objective-c
typedef enum UIModalTransitionStyle : NSInteger {
    UIModalTransitionStyleCoverVertical = 0,	// 垂直覆盖
    UIModalTransitionStyleFlipHorizontal,		// 水平翻转
    UIModalTransitionStyleCrossDissolve,		// 淡入淡出
    UIModalTransitionStylePartialCurl			// 翻页卷曲
} UIModalTransitionStyle;
```



### 显示半模态视图

```objective-c
ModelViewController *vc = [[ModelViewController alloc] init];

// 是否由当前视图控制器的视图显示被模态推出的视图，否则默认由根视图控制器来显示
//    vc.definesPresentationContext = YES;

// 设置模态显示样式
vc.modalPresentationStyle = UIModalPresentationOverCurrentContext;
// 设置视图转换动画样式,默认垂直覆盖
vc.modalTransitionStyle = UIModalTransitionStyleCoverVertical;

vc.dismissBlock = ^{
    // 恢复 presenting VC 视图的背景
    UIColor *backColor = [UIColor whiteColor];
    self.view.backgroundColor = backColor;
};

// 设置 presenting VC 视图的背景
UIColor *whiteColor = [UIColor whiteColor];
self.view.backgroundColor =
    [whiteColor colorWithAlphaComponent:0.7];
[self presentViewController:vc animated:YES completion:nil];
```



ModelViewController.m:

```objective-c
#import "ModelViewController.h"

@interface ModelViewController ()
  
@property (weak, nonatomic) IBOutlet UIView *coverView;

@end

@implementation ModelViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view from its nib.
    
    // 单击手势
    UITapGestureRecognizer *tapRecognizer =
    [[UITapGestureRecognizer alloc] initWithTarget:self
                                            action:@selector(tap:)];
    
    [self.coverView addGestureRecognizer:tapRecognizer];
}

- (void)didReceiveMemoryWarning {
    [super didReceiveMemoryWarning];
    // Dispose of any resources that can be recreated.
}

- (IBAction)dismissViewController:(id)sender {
    self.dismissBlock();
    [self.presentingViewController dismissViewControllerAnimated:YES
                                                      completion:nil];
}

- (void)tap:(id)sender {
    self.dismissBlock();
    [self.presentingViewController dismissViewControllerAnimated:YES
                                                      completion:nil];
}
```



![](http://upload-images.jianshu.io/upload_images/2648731-2933635cff90c51d.gif?imageMogr2/auto-orient/strip)
