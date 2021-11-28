> 使用 `AVFoundation` 框架识别解码二维码


### 参考

- [GitHub 源码：shinobicontrols/iOS7-day-by-day](https://github.com/ShinobiControls/iOS7-day-by-day)
- [天天品尝iOS7甜点 :: Day 16 :: Decoding QR Codes with AVFoundation](http://blog.kingiol.com/2014/01/20/ios7-day-by-day-day16-decoding-qr-codes-with-avfoundation/)
- [iOS-原生扫描登录](http://www.cocoachina.com/ios/20180206/22170.html) AVCaptureDevice、AVCaptureSession、AVCaptureInput、AVCaptureOutput、AVCaptureVideoPreviewLayer 介绍及示例


### Drawing the code outline - 给二维码添加上线框

这个类用于实时绘制二维码边框

```objective-c
// *********************************************
// SCShapeView.h
#import <UIKit/UIKit.h>

/**
 给二维码添加线框
 */
@interface SCShapeView : UIView

// 封装了一系列点
@property (nonatomic, strong) NSArray *corners;

@end
  
// *********************************************
// SCShapeView.m

#import "SCShapeView.h"

@interface SCShapeView () {
    CAShapeLayer *_outline; // 用于绘制线框的图层
}
@end

@implementation SCShapeView

- (id)initWithFrame:(CGRect)frame
{
    self = [super initWithFrame:frame];
    if (self) {
        // Initialization code
        _outline = [CAShapeLayer new];
        _outline.strokeColor = [[[UIColor greenColor] colorWithAlphaComponent:0.8] CGColor];
        _outline.lineWidth = 2.0;
        _outline.fillColor = [[UIColor clearColor] CGColor];
        [self.layer addSublayer:_outline];
    }
    return self;
}

- (void)setCorners:(NSArray *)corners
{
    if(corners != _corners) {
        _corners = corners;
        // 如果corners属性发生了变化，图形就将会使用新的位置进行绘制
        _outline.path = [[self createPathFromPoints:corners] CGPath];
    }
}

// 通过封装了CGPoint对象的数组对象来创建 UIBezierPath
- (UIBezierPath *)createPathFromPoints:(NSArray *)points
{
    UIBezierPath *path = [UIBezierPath new];
    // 起点
    [path moveToPoint:[[points firstObject] CGPointValue]];
    
    // 添加线
    for (NSUInteger i = 1; i < [points count]; i++) {
        [path addLineToPoint:[points[i] CGPointValue]];
    }
    
    // 返回到起点
    [path addLineToPoint:[[points firstObject] CGPointValue]];
    
    return path;
}

@end
```



### 捕获并显示二维码

```objective-c
// *********************************************
// SCViewController.h
#import <UIKit/UIKit.h>

@interface SCViewController : UIViewController

@end

// *********************************************
// SCViewController.m
#import "SCViewController.h"
@import AVFoundation;
#import "SCShapeView.h"

@interface SCViewController () <AVCaptureMetadataOutputObjectsDelegate> {
    AVCaptureVideoPreviewLayer *_previewLayer; // 预览显示摄像内容
    SCShapeView *_boundingBox; // 二维码边框视图，用于实时绘制被扫描二维码的边框
    NSTimer *_boxHideTimer;    // 隐藏二维码边框的计时器
    UILabel *_decodedMessage;  // label标签，用于显示识别出的二维码信息
}
@end

@implementation SCViewController

#pragma mark - Lifecycle

- (void)viewDidLoad {
    [super viewDidLoad];
    
    // 创建 AVCaptureSession 实例对象
    AVCaptureSession *session = [[AVCaptureSession alloc] init];
    // 创建输入设备：后置摄像头
    AVCaptureDevice *device = [AVCaptureDevice defaultDeviceWithMediaType:AVMediaTypeVideo];
    // 创建输入对象
    NSError *error = nil;
    AVCaptureDeviceInput *input = [AVCaptureDeviceInput deviceInputWithDevice:device error:&error];
    if(input) {
        // 将输入对象添加进 Session 会话中
        [session addInput:input];
    } else {
        NSLog(@"error: %@", error);
        return;
    }
    
    // 创建并添加输出对象
    // AVCaptureMetadataOutput 是 AVCaptureOutput 的子类,用于输出元数据本身
    AVCaptureMetadataOutput *output = [[AVCaptureMetadataOutput alloc] init];
    // Have to add the output before setting metadata types
    [session addOutput:output];
    // What different things can we register to recognise?
    NSLog(@"%@", [output availableMetadataObjectTypes]);
    // 设置输出对象元数据类型
    [output setMetadataObjectTypes:@[AVMetadataObjectTypeQRCode]];
    // 设置代理：当前视图控制器，并在主线程调用
    [output setMetadataObjectsDelegate:self queue:dispatch_get_main_queue()];
    
    // 显示当前视图中的输出内容 —— 实时显示摄像内容
    // AVCaptureVideoPreviewLayer 是 CALayer 的子类
    _previewLayer = [AVCaptureVideoPreviewLayer layerWithSession:session];
    _previewLayer.videoGravity = AVLayerVideoGravityResizeAspectFill;
    _previewLayer.bounds = self.view.bounds;
    _previewLayer.position = CGPointMake(CGRectGetMidX(self.view.bounds), CGRectGetMidY(self.view.bounds));
    [self.view.layer addSublayer:_previewLayer];
    
    // 添加视图来绘制 UIView 的边框 —— 给二维码添加线框
    _boundingBox = [[SCShapeView alloc] initWithFrame:self.view.bounds];
    _boundingBox.backgroundColor = [UIColor clearColor];
    _boundingBox.hidden = YES;
    [self.view addSubview:_boundingBox];
    
    // 添加标签用来显示结果数据
    _decodedMessage = [[UILabel alloc] initWithFrame:CGRectMake(0, CGRectGetHeight(self.view.bounds) - 75, CGRectGetWidth(self.view.bounds), 75)];
    _decodedMessage.numberOfLines = 0;
    _decodedMessage.backgroundColor = [UIColor colorWithWhite:0.8 alpha:0.9];
    _decodedMessage.textColor = [UIColor darkGrayColor];
    _decodedMessage.textAlignment = NSTextAlignmentCenter;
    [self.view addSubview:_decodedMessage];
    
    // 运行 AVSession 会话
    [session startRunning];
}

#pragma mark - AVCaptureMetadataOutputObjectsDelegate
- (void)captureOutput:(AVCaptureOutput *)captureOutput didOutputMetadataObjects:(NSArray *)metadataObjects fromConnection:(AVCaptureConnection *)connection
{
    for (AVMetadataObject *metadata in metadataObjects) {
        if ([metadata.type isEqualToString:AVMetadataObjectTypeQRCode]) {
            
            // 将元数据坐标转换为屏幕坐标
            AVMetadataMachineReadableCodeObject *transformed = (AVMetadataMachineReadableCodeObject *)[_previewLayer transformedMetadataObjectForMetadataObject:metadata];
            
            // Update the frame on the _boundingBox view, and show it
            _boundingBox.frame = transformed.bounds;
            _boundingBox.hidden = NO;
            // Now convert the corners array into CGPoints in the coordinate system
            //  of the bounding box itself
            NSArray *translatedCorners = [self translatePoints:transformed.corners
                                                      fromView:self.view
                                                        toView:_boundingBox];
            
            // Set the corners array
            _boundingBox.corners = translatedCorners;
            
            // Update the view with the decoded text
            _decodedMessage.text = [transformed stringValue];
            
            // Start the timer which will hide the overlay
            [self startOverlayHideTimer];
        }
    }
}

#pragma mark - Utility Methods
- (void)startOverlayHideTimer
{
    // Cancel it if we're already running
    if(_boxHideTimer) {
        [_boxHideTimer invalidate];
    }
    
    // Restart it to hide the overlay when it fires
    // 把绘制出来的线框过一段时间消失掉。
    // 这就阻止了当前没有发现二维码信息的时候，线框还存在的问题。
    _boxHideTimer = [NSTimer scheduledTimerWithTimeInterval:0.2
                                                     target:self
                                                   selector:@selector(removeBoundingBox:)
                                                   userInfo:nil
                                                    repeats:NO];
}

- (void)removeBoundingBox:(id)sender
{
    // Hide the box and remove the decoded text
    _boundingBox.hidden = YES;
    _decodedMessage.text = @"";
}

/*
 1.转换坐标格式
 2.坐标系映射，当前页面 -> boxView
 
 原始坐标格式：
 {
 X = "115.2051412322095";
 Y = "135.4776067211339";
 },

 转换后坐标格式：
 "NSPoint: {8.58306884765625e-06, 0.95792221069336847}",
*/
- (NSArray *)translatePoints:(NSArray *)points fromView:(UIView *)fromView toView:(UIView *)toView
{
    NSMutableArray *translatedPoints = [NSMutableArray new];

    // The points are provided in a dictionary with keys X and Y
    for (NSDictionary *point in points) {
        // Let's turn them into CGPoints
        CGPoint pointValue = CGPointMake([point[@"X"] floatValue], [point[@"Y"] floatValue]);
        // Now translate from one view to the other
        CGPoint translatedPoint = [fromView convertPoint:pointValue toView:toView];
        // Box them up and add to the array
        [translatedPoints addObject:[NSValue valueWithCGPoint:translatedPoint]];
    }
    
    return [translatedPoints copy];
}


@end
```

效果

![](http://upload-images.jianshu.io/upload_images/2648731-35bf6c7e2f5db733.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### 第三方的二维码开源库

* [ZXingObjC](https://github.com/TheLevelUp/ZXingObjC)——（⭐️2500+）支持多种格式的条码库。
* [LBXScan](https://github.com/MxABC/LBXScan) —— （⭐️2000+）👍 A barcode and qr code scanner (二维码、扫码、扫一扫、ZXing和ios系统自带扫码封装，扫码界面效果封装)(Objective-C和Swift均支持).
* [原生实现扫描二维码条码](http://code.cocoachina.com/view/129108) —— iOS原生实现扫描二维码条码.
* [ZFScan](https://github.com/Zirkfied/ZFScan) —— （⭐️85+）仿微信 二维码/条形码 扫描.
* [QRCatcher](https://github.com/100mango/QRCatcher) ——（⭐️270+）完整项目，一个简洁美观的二维码扫描应用， [iOS学习：AVFoundation 视频流处理--二维码].
* [BarcodeScanner](https://github.com/hyperoslo/BarcodeScanner) —— （⭐️780+）带状态控制的条码扫描库，支持处理相机权限、自定义颜色和提示信息，不依赖其他第三方库)、*Swift*
* [MQRCodeReaderViewController](https://github.com/zhengjinghua/MQRCodeReaderViewController) ——（⭐️352+）二维码扫描控件.
* [QRWeiXinDemo](https://github.com/lcddhr/QRWeiXinDemo) —— （⭐️221+）仿微信二维码扫描，中间透明区域.
* [EFQRCode](https://github.com/EyreFree/EFQRCode) ——（⭐️2084+）iOS 花式二维码生成库、*Swift*
