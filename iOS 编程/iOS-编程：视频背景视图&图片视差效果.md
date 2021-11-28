# 视频背景视图

![](http://upload-images.jianshu.io/upload_images/2648731-84d790a723495c86.gif?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 实现方式一：使用WKWebView

使用 **WKWebView** 实现视频背景视图：

1. **WKWebView** 直接加载 gif 图片；
2. **WKWebView** 加载 HTML 页面，HTML页面中包含 gif 图片；

使用方法2: 用 HTML 页面加载 gif 图片而不是直接加载 gif 图片的原因是，在 HTML 中可以通过 CSS 将图片全屏显示，还可以保持图片的原始比例。

### STEP 1: 使用 [GIF Brewery](https://gfycat.com/gifbrewery) 将视频文件转换为 gif 图片

### STEP 2: 制作HTML页面

WebViewContent.html

```html
<!DOCTYPE html>
<html style="height: 100%;">
<head>
	<meta charset="utf-8">
	<title>含有GIF的web页面</title>
</head>
<body style="margin:0; height: 100%">
	<div style="background: url(behance.gif) center center / cover no-repeat; height: 100%" ></div>
</body>
</html>
```



### STEP 3: 使用 **WKWebView** 加载并显示HTML页面，并且将 **WKWebView** 作为背景视图：

```objective-c
- (void)viewDidLoad {
    [super viewDidLoad];
        
    // 加载含有 gif 图片的 HTML 文件
    NSString *htmlPath = [[NSBundle mainBundle] pathForResource:@"WebViewContent" ofType:@"html"];
    NSURL *htmlURL = [[NSURL alloc] initFileURLWithPath:htmlPath];
    NSData *htmlData = [[NSData alloc] initWithContentsOfURL:htmlURL];
    
    // 使用 WKWebView 视图加载并显示 gif 图片
    WKWebView *webView = [[WKWebView alloc] initWithFrame:self.view.frame];
    [webView loadData:htmlData MIMEType:@"text/html" characterEncodingName:@"UTF-8" baseURL:[htmlURL URLByDeletingLastPathComponent]];
    webView.userInteractionEnabled = NO;
    [self.view addSubview:webView];
    
    // 遮罩层视图
    UIView *filter = [[UIView alloc] initWithFrame:self.view.frame];
    filter.backgroundColor = [UIColor blackColor];
    filter.alpha = 0.05;
    [self.view addSubview:filter];
}
```



## 实现方式二：使用[YYImage](https://github.com/ibireme/YYImage)

YYImage 是一个功能强大的 iOS 图像框架，它支持 WebP, PNG, GIF, JPEG, JP2, TIFF, BMP, ICO, ICNS 等图片的播放/编码/解码操作。

显示动画类型的图片：

```objective-c
UIImage *image = [YYImage imageNamed:@"railway.gif"];
UIImageView *imageView = [[YYAnimatedImageView alloc] initWithImage:image];
imageView.frame = self.view.bounds;
imageView.contentMode = UIViewContentModeScaleAspectFill;
[self.view addSubview:imageView];
```

## 实现方式三：使用 AVFoundation
* 参考：[仿 Uber 视频背景登录界面以及登录动画](https://www.jianshu.com/p/d0b8a96544f3)
示例代码：

实现步骤：
1. AVPlayerItem 加载视频文件；
2. AVPlayer 用于播放 AVPlayerItem，并进行一些播放设置，设置播放完成后自动重新播放的通知；
3. 将播放图层 AVPlayerLayer 添加到当前视图图层上；
4. 应用从前台切换到后台时会停止播放视频，因此，当应用从后台恢复到前台状态时，需要恢复播放视频，需要设置一个全局的通知。

```objective-c
#import "ViewController.h"

// 1.需要导入 AVFoundation 框架
#import <AVFoundation/AVFoundation.h>

@interface ViewController ()

/** 全屏播放器 */
@property (nonatomic, strong) AVPlayer *player;

@end

@implementation ViewController

#pragma mark - Lifecycle

- (void)viewDidLoad {
    [super viewDidLoad];

    [self setupForAVplayerView];
}

- (void)viewWillAppear:(BOOL)animated {
    // 视频播放通知
    [[NSNotificationCenter defaultCenter] addObserver:self
                                             selector:@selector(playVideos)
                                                 name:@"videoshouldplay"
                                               object:nil];
}

#pragma mark - Custom Accessors

- (AVPlayer *)player {
    if (!_player) {
        
        // AVPlayerItem
        NSString *filePath = [[NSBundle mainBundle] pathForResource:@"BridgeLoop-640p" ofType:@"mp4"];
        NSURL *url = [NSURL fileURLWithPath:filePath];
        AVPlayerItem *playerItem = [AVPlayerItem playerItemWithURL:url];
        
        // AVPlayer
        _player = [AVPlayer playerWithPlayerItem:playerItem];
        // 设置重复播放
        _player.actionAtItemEnd = AVPlayerActionAtItemEndNone;
        // 播放结束时的通知
        [[NSNotificationCenter defaultCenter]addObserver:self
                                                selector:@selector(playerItemDidPlayToEndTimeNotification:)
                                                    name:AVPlayerItemDidPlayToEndTimeNotification
                                                object:nil];
    }
    return _player;
}

#pragma mark - Private

- (void)setupForAVplayerView {
    //创建播放图层
    AVPlayerLayer *playerLayer = [AVPlayerLayer playerLayerWithPlayer:self.player];
    playerLayer.frame = self.view.bounds;
    [self.view.layer addSublayer:playerLayer];
}

- (void)playVideos {
    [self.player play];
}

// 播放结束通知
- (void)playerItemDidPlayToEndTimeNotification:(NSNotification *)sender {
    [_player seekToTime:kCMTimeZero]; // 播放结束后，设置重新播放
}

@end
```

当应用在前后台之间切换时，播放会暂停，因此每当应用进入前台时，需要发送通知：

```objective-c
// AppDelegate.m
- (void)applicationDidBecomeActive:(UIApplication *)application {
    // Restart any tasks that were paused (or not yet started) while the application was inactive. If the application was previously in the background, optionally refresh the user interface.
    [[NSNotificationCenter defaultCenter] postNotificationName:@"videoshouldplay" object:nil];
}
```




# 图片视差效果

iOS用户界面有一个动态效果，背景图片会随着手机的水平晃动而跟随晃动。

打开/关闭此功能：设置—通用—辅助功能—减弱动态效果。

![](http://upload-images.jianshu.io/upload_images/2648731-e3a92890f4de552c.gif?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


如果你在iOS中安装了一个叫做 **红板报** 的新闻类应用，那么你应该注意到，它的「封面故事」中的图片也有视差效果：

![红板报：手机沿各个方向倾斜的视差效果](http://upload-images.jianshu.io/upload_images/2648731-cbeabde2d4a1caa5.gif?imageMogr2/auto-orient/strip)



为图片添加视差效果很简单，以下是如何实现此功能的示例代码：

```objective-c
// 添加图片
UIImage *image = [UIImage imageNamed:@"albume.png"];
UIImageView *imageView = [[UIImageView alloc] initWithImage:image];
imageView.frame = self.view.bounds;
imageView.contentMode = UIViewContentModeScaleAspectFill;
[self.view addSubview:imageView];

// 图片视差效果：水平方向
UIInterpolatingMotionEffect *effectX = [[UIInterpolatingMotionEffect alloc] initWithKeyPath:@"center.x" type:UIInterpolatingMotionEffectTypeTiltAlongHorizontalAxis];
effectX.maximumRelativeValue = @(-50);
effectX.minimumRelativeValue = @(50);
[imageView addMotionEffect:effectX];

// 图片视差效果：垂直方向
UIInterpolatingMotionEffect *effectY = [[UIInterpolatingMotionEffect alloc] initWithKeyPath:@"center.y" type:UIInterpolatingMotionEffectTypeTiltAlongVerticalAxis];
effectY.maximumRelativeValue = @(-50);
effectY.minimumRelativeValue = @(50);
[imageView addMotionEffect:effectY];
```

![](http://upload-images.jianshu.io/upload_images/2648731-e707ebea9098398e.gif?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




### 参考：

* [iOS: Make an Awesome Video Background View Using UIWebView (Objective-C & Swift 3.0)](https://medium.com/swift-programming/ios-make-an-awesome-video-background-view-objective-c-swift-318e1d71d0a2)
* [iOS实现墙纸的运动视差效果 @[阳光下慵懒的驴](https://www.jianshu.com/u/847e6ce9c4aa)](https://www.jianshu.com/p/27d3fa227cfa)
