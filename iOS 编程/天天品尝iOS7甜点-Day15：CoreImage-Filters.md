> 本文主要内容：为图像添加滤镜效果、字符串生存二维码
>

### 参考

- [GitHub 源码：shinobicontrols/iOS7-day-by-day](https://github.com/ShinobiControls/iOS7-day-by-day)
- [天天品尝iOS7甜点 :: Day 15 :: CoreImage Filters](http://blog.kingiol.com/2014/01/19/ios7-day-by-day-day15-coreimage-filters/)



## 一、Photo Effect Filters - 为图像添加滤镜效果

代码：

```objective-c
// 1.创建 CIImage 对象：UIImage → CGImage → CIImage
UIImage *image = [UIImage imageNamed:@"iu.png"];
CIImage *inputImage = [CIImage imageWithCGImage:[image CGImage]];

// 2.1 创建 CIFilter 过滤器对象
CIFilter *filter = [CIFilter filterWithName:@"CIPhotoEffectChrome"];
// 2.2 KVC 设置过滤参数
[filter setValue:inputImage forKey:kCIInputImageKey];

// 3.1 输出方法一： CIImage → UIImage
UIImage *outputImage1 = [UIImage imageWithCIImage:filter.outputImage];
self.imageView.image = outputImage1;

// 3.2 输出方法二： CGImage → UIImage
CGImageRef cgImage = [[CIContext contextWithOptions:nil] createCGImage:filter.outputImage fromRect:filter.outputImage.extent];
UIImage *outputImage2 = [UIImage imageWithCGImage:cgImage];
CGImageRelease(cgImage);
self.imageView.image = outputImage2;
```

原图：

![听说点赞❤️就可以把IU还给你](http://upload-images.jianshu.io/upload_images/2648731-692fe63c3ecca484.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

滤镜效果：

![对不起，把你滤丑了](http://upload-images.jianshu.io/upload_images/2648731-a0c32ebf4541824b.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![要怼就怼史蒂夫，这是他们家的原生框架，我只是个搬运工](http://upload-images.jianshu.io/upload_images/2648731-fdd610e5ffb39470.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### **CIFilter** 过滤器类型：

```objective-c
@"CIPhotoEffectChrome"
@"CIPhotoEffectFade"
@"CIPhotoEffectInstant"
@"CIPhotoEffectMono"
@"CIPhotoEffectNoir"
@"CIPhotoEffectProcess"
@"CIPhotoEffectTonal"
@"CIPhotoEffectTransfer"
```

详细可参阅官方文档：[CoreImage Filter Reference](https://developer.apple.com/library/content/documentation/GraphicsImaging/Reference/CoreImageFilterReference/#//apple_ref/doc/filter/ci/CIPhotoEffectChrome)



## 二、QR Code Generation - 生成二维码

```objective-c
- (void)viewDidLoad {
    [super viewDidLoad];
    
    // 1. 待编码字符串
    NSString *string = @"http://www.jianshu.com/u/84c5fadc7c18";
    // 2. 生成二维码
    CIImage *qrCode = [self createQRForString:string];
    // 3. 调整图像大小并显示
    UIImage *qrCodeImg = [self createNonInterpolatedUIImageFormCIImage:qrCode withScale:[[UIScreen mainScreen] scale] * 2];
    self.imageView.image = qrCodeImg;
}

// 生成二维码方法
- (CIImage *)createQRForString:(NSString *)qrString {
    // 1. 首先把 NSString 对象使用 UTF-8 编码转换成 NSData 对象
    NSData *stringData = [qrString dataUsingEncoding:NSUTF8StringEncoding];
    
    // 2. 创建 CIFilter 过滤器对象
    CIFilter *qrFilter = [CIFilter filterWithName:@"CIQRCodeGenerator"];
    
    // 3.1 设置 inputMessage （即输入内容）
    [qrFilter setValue:stringData forKey:@"inputMessage"];
    // 3.2 设置生成二维码的误差等级
    [qrFilter setValue:@"H" forKey:@"inputCorrectionLevel"];
    
    // 4. 输出为 CIImage
    return qrFilter.outputImage;
}

// 默认输出的 CIImage 是一个 1pt 的最小的分辨率的 CIImage 对象。
// 我们需要重设这个图像的大小，但是我们并不想对它进行篡改。
// 调整图像大小
- (UIImage *)createNonInterpolatedUIImageFormCIImage:(CIImage *)image withScale:(CGFloat) scale {
    // Render the CIImage into a CGImage
    CGImageRef cgImage = [[CIContext contextWithOptions:nil] createCGImage:image fromRect:image.extent];
    
    // Now we'll rescale using CoreGraphics
    UIGraphicsBeginImageContext(CGSizeMake(image.extent.size.width * scale, image.extent.size.width * scale));
    CGContextRef context = UIGraphicsGetCurrentContext();
    // We don't want to interpolate (since we've got a pixel-corrent image)
    CGContextSetInterpolationQuality(context, kCGInterpolationNone);
    CGContextDrawImage(context, CGContextGetClipBoundingBox(context), cgImage);
    // Get the image out
    UIImage *scaledImage = UIGraphicsGetImageFromCurrentImageContext();
    // Tidy up
    UIGraphicsEndImageContext();
    CGImageRelease(cgImage);
    return scaledImage;
}
```

生成的二维码：

![](http://upload-images.jianshu.io/upload_images/2648731-a09712fdc821e0b7.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)



### inputCorrectionLevel - 二维码等级

* L - 7%的错误弹性
* M - 15%的错误弹性
* Q - 25%的错误弹性
* H - 30%的错误弹性
