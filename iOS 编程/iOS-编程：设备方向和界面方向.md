### 设备方向（device orientation）

设备方向指的是设备的物理方向。

```objectivec
typedef enum UIDeviceOrientation : NSInteger {
    UIDeviceOrientationUnknown,				// 未知方向
    UIDeviceOrientationPortrait,			// 正的竖排方向
    UIDeviceOrientationPortraitUpsideDown,	// 倒置方向
    UIDeviceOrientationLandscapeLeft,		// 左旋转方向
    UIDeviceOrientationLandscapeRight,		// 右旋转方向
    UIDeviceOrientationFaceUp,				// 正面朝上
    UIDeviceOrientationFaceDown				// 正面朝下
} UIDeviceOrientation;
```

### 界面方向（interface orientation）

界面方向指的是用户所看到的应用界面的方向。

```objectivec
typedef enum UIInterfaceOrientation : NSInteger {
  	// 未知方向
    UIInterfaceOrientationUnknown = UIDeviceOrientationUnknown,
  	// 竖排方向，主屏幕按钮位于屏幕下方
    UIInterfaceOrientationPortrait = UIDeviceOrientationPortrait,
    // 竖排方向，主屏幕按钮位于屏幕上方
    UIInterfaceOrientationPortraitUpsideDown = UIDeviceOrientationPortraitUpsideDown,
    // 横排方向，主屏幕按钮位于屏幕右侧
    UIInterfaceOrientationLandscapeLeft = UIDeviceOrientationLandscapeRight,
  	// 横排方向，主屏幕按钮位于屏幕左侧
    UIInterfaceOrientationLandscapeRight = UIDeviceOrientationLandscapeLeft
} UIInterfaceOrientation;
```

### 示例代码

```objectivec
#import "ViewController.h"

@interface ViewController ()

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    // 通知中心是专门供程序中，不同类间的消息通信而设置的。这里用来捕捉手机方向切换的事件
    [[NSNotificationCenter defaultCenter] addObserver:self
                                             selector:@selector(orientationChanged)
                                                 name:UIDeviceOrientationDidChangeNotification
                                               object:nil];
}

- (void)orientationChanged {
    switch ([[UIDevice currentDevice] orientation]) {
        case UIDeviceOrientationPortrait:
            NSLog(@"设备垂直,Home按钮在底部！");
            break;
        case UIDeviceOrientationPortraitUpsideDown:
            NSLog(@"设备垂直,Home按钮在顶部！");
            break;
        case UIDeviceOrientationLandscapeLeft:
            NSLog(@"设备水平，Home按钮在右边！");
            break;
        case UIDeviceOrientationLandscapeRight:
            NSLog(@"设备水平，Home按钮在左边！");
            break;
        case UIDeviceOrientationFaceUp:
            NSLog(@"设备平躺，正面朝上！");
            break;
        case UIDeviceOrientationFaceDown:
            NSLog(@"设备平躺，正面朝下！");
            break;
        default:
            break;
    }
}
```
