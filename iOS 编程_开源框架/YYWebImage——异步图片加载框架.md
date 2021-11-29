# 前言

在项目源码的 **YYWebImageOperation.m** 第 377 行：

```objectivec
 // request image from web
 // ...
 _connection = [[NSURLConnection alloc] initWithRequest:_request delegate:[_YYWebImageWeakProxy proxyWithTarget:self]];
```

该框架在加载网络图片时，请求网络使用的类仍然是：`NSURLConnection`。

而在 Apple 的 **NSURLConnection.h** 头文件中早已明确声明此类不应该再使用了。
```objectivec
/*** DEPRECATED: The NSURLConnection class should no longer be used.  NSURLSession is the replacement for NSURLConnection ***/
```

**NSURLSession** 是 iOS 7 引入的特性。

因此，在项目还未更新到使用  `NSURLSession` 之前，不建议在项目生产环境下使用该库（考虑到后期迁移成本太高）。



# README 文档

![progressive 渐进式加载](https://upload-images.jianshu.io/upload_images/2648731-97eaba1ad62ab748.gif?imageMogr2/auto-orient/strip)


YYWebImage 是一个异步图片加载框架 ([YYKit](https://github.com/ibireme/YYKit) 组件之一).

其设计目的是试图替代 SDWebImage、PINRemoteImage、FLAnimatedImage 等开源框架，它支持这些开源框架的大部分功能，同时增加了大量新特性、并且有不小的性能提升。

它底层用 [YYCache](https://github.com/ibireme/YYCache) 实现了内存和磁盘缓存, 用 [YYImage](https://github.com/ibireme/YYImage) 实现了 WebP/APNG/GIF 动图的解码和播放。
你可以查看这些项目以获得更多信息。



# 特性

- 异步的图片加载，支持 HTTP 和本地文件。
- 支持 GIF、APNG、WebP 动画（动态缓存，低内存占用）。
- 支持逐行扫描、隔行扫描、渐进式图像加载。
- UIImageView、UIButton、MKAnnotationView、CALayer 的 Category 方法支持。
- 常见图片处理：模糊、圆角、大小调整、裁切、旋转、色调等。
- 高性能的内存和磁盘缓存。
- 高性能的图片设置方式，以避免主线程阻塞。
- 每个类和方法都有完善的文档注释。

用法
==============

### 从 URL 加载图片
```objectivec
// 加载网络图片
imageView.yy_imageURL = [NSURL URLWithString:@"http://github.com/logo.png"];
	
// 加载本地图片
imageView.yy_imageURL = [NSURL fileURLWithPath:@"/tmp/logo.png"];
```

#### 从 URL 加载图片的方法

```objectivec
- (void)yy_setImageWithURL:(nullable NSURL *)imageURL 
               placeholder:(nullable UIImage *)placeholder;

- (void)yy_setImageWithURL:(nullable NSURL *)imageURL 
                   options:(YYWebImageOptions)options;

- (void)yy_setImageWithURL:(nullable NSURL *)imageURL
               placeholder:(nullable UIImage *)placeholder
                   options:(YYWebImageOptions)options
                completion:(nullable YYWebImageCompletionBlock)completion;

- (void)yy_setImageWithURL:(nullable NSURL *)imageURL
               placeholder:(nullable UIImage *)placeholder
                   options:(YYWebImageOptions)options
                  progress:(nullable YYWebImageProgressBlock)progress
                 transform:(nullable YYWebImageTransformBlock)transform
                completion:(nullable YYWebImageCompletionBlock)completion;

- (void)yy_setImageWithURL:(nullable NSURL *)imageURL
               placeholder:(nullable UIImage *)placeholder
                   options:(YYWebImageOptions)options
                   manager:(nullable YYWebImageManager *)manager
                  progress:(nullable YYWebImageProgressBlock)progress
                 transform:(nullable YYWebImageTransformBlock)transform
                completion:(nullable YYWebImageCompletionBlock)completion;

// highlightedImage
- (void)yy_setHighlightedImageWithURL:(nullable NSURL *)imageURL 
                          placeholder:(nullable UIImage *)placeholder;

- (void)yy_setHighlightedImageWithURL:(nullable NSURL *)imageURL 
                              options:(YYWebImageOptions)options;

- (void)yy_setHighlightedImageWithURL:(nullable NSURL *)imageURL
                          placeholder:(nullable UIImage *)placeholder
                              options:(YYWebImageOptions)options
                           completion:(nullable YYWebImageCompletionBlock)completion;

- (void)yy_setHighlightedImageWithURL:(nullable NSURL *)imageURL
                          placeholder:(nullable UIImage *)placeholder
                              options:(YYWebImageOptions)options
                             progress:(nullable YYWebImageProgressBlock)progress
                            transform:(nullable YYWebImageTransformBlock)transform
                           completion:(nullable YYWebImageCompletionBlock)completion;

- (void)yy_setHighlightedImageWithURL:(nullable NSURL *)imageURL
                          placeholder:(nullable UIImage *)placeholder
                              options:(YYWebImageOptions)options
                              manager:(nullable YYWebImageManager *)manager
                             progress:(nullable YYWebImageProgressBlock)progress
                            transform:(nullable YYWebImageTransformBlock)transform
                           completion:(nullable YYWebImageCompletionBlock)completion;
```



#### YYWebImageOptions - 控制图片操作的选项

```objectivec
/// 控制图像操作的选项.
typedef NS_OPTIONS(NSUInteger, YYWebImageOptions) {
    
    /// 下载图像时在状态栏上显示网络活动指示器。
    YYWebImageOptionShowNetworkActivity = 1 << 0,
    
    /// 在下载期间显示渐进/隔行扫描/基线图像（与Web浏览器相同）。
    /// progressive/interlaced/baseline
    YYWebImageOptionProgressive = 1 << 1,
    
    /// 在下载过程中显示模糊的逐行JPEG或隔行扫描PNG图像。
    /// 这将忽略基线图像以获得更好的用户体验。
    YYWebImageOptionProgressiveBlur = 1 << 2,
    
    /// 使用 NSURLCache 而不是 YYImageCache
    YYWebImageOptionUseNSURLCache = 1 << 3,
    
    /// 允许不可信的 SSL 证书。
    YYWebImageOptionAllowInvalidSSLCertificates = 1 << 4,
    
    /// 允许应用程序处于后台状态时在后台下载图像。
    YYWebImageOptionAllowBackgroundTask = 1 << 5,
    
    /// 处理存储在 NSHTTPCookieStore 中的 Cookie。
    YYWebImageOptionHandleCookies = 1 << 6,
    
    /// 从远程加载映像并刷新映像缓存。
    YYWebImageOptionRefreshImageCache = 1 << 7,
    
    /// 不要将图像从磁盘中加载，或缓存到磁盘。
    YYWebImageOptionIgnoreDiskCache = 1 << 8,
    
    /// 在设置新的 URL 之前，不要更改视图的图像。
    YYWebImageOptionIgnorePlaceHolder = 1 << 9,
    
    /// 忽略图像解码。
    /// This may used for image downloading without display.
    YYWebImageOptionIgnoreImageDecoding = 1 << 10,
    
    /// 忽略多帧图像解码。
    /// This will handle the GIF/APNG/WebP/ICO image as single frame image.
    YYWebImageOptionIgnoreAnimatedImage = 1 << 11,
    
    /// 使用淡入淡出动画设置图像。
    /// This will add a "fade" animation on image view's layer for better user experience.
    YYWebImageOptionSetImageWithFadeAnimation = 1 << 12,
    
    /// 图像提取完成时，请勿将图像设置为视图。
    /// You may set the image manually.
    YYWebImageOptionAvoidSetImage = 1 << 13,
    
    /// 当 URL 无法下载时，此标志将 URL 添加到黑名单（内存中）
    /// 这样系统就不会无限尝试下载
    YYWebImageOptionIgnoreFailedURL = 1 << 14,
};
```



### 加载动图

```objectivec
// 只需要把 `UIImageView` 替换为 `YYAnimatedImageView` 即可。
UIImageView *imageView = [YYAnimatedImageView new];
imageView.yy_imageURL = [NSURL URLWithString:@"http://github.com/ani.webp"];
```



### 渐进式图片加载

```objectivec
// 渐进式：边下载边显示
[imageView yy_setImageWithURL:url options:YYWebImageOptionProgressive];
	
// 渐进式加载，增加模糊效果和渐变动画 （见本页最上方的GIF演示）
[imageView yy_setImageWithURL:url options:YYWebImageOptionProgressiveBlur ｜ YYWebImageOptionSetImageWithFadeAnimation];
```



### 加载、处理图片

```objectivec
// 1. 下载图片
// 2. 获得图片下载进度
// 3. 调整图片大小、加圆角
// 4. 显示图片时增加一个淡入动画，以获得更好的用户体验
	
[imageView yy_setImageWithURL:url
   placeholder:nil
   options:YYWebImageOptionSetImageWithFadeAnimation // 淡入动画
   progress:^(NSInteger receivedSize, NSInteger expectedSize) {
       // 获得图片下载进度
       progress = (float)receivedSize / expectedSize;
   }
   transform:^UIImage *(UIImage *image, NSURL *url) {
       // 调整图片大小
       image = [image yy_imageByResizeToSize:CGSizeMake(100, 100) contentMode:UIViewContentModeCenter];
       // 切圆角
       return [image yy_imageByRoundCornerRadius:10];
   }
   completion:^(UIImage *image, NSURL *url, YYWebImageFromType from, YYWebImageStage stage, NSError *error) {
       if (from == YYWebImageFromDiskCache) {
           // 如果图片是从已缓存的磁盘中加载进来的，执行这里的操作
           NSLog(@"load from disk cache");
       }
   }];
```

Tips： 如果图片下载完成后，在 transform Block 中调整图片大小、加圆角无效，可能是缓存的原因，先选择 `YYWebImageOptionRefreshImageCache` 刷新缓存或者 `YYWebImageOptionIgnoreDiskCache` 忽略缓存。



### 加载、处理图片示例 2

```objectivec
// 设置图片并切圆角
[self.imgView setImageWithURL:_data.imageURL
                  placeholder:[UIImage imageNamed:@"image_default"]
                      options:YYWebImageOptionSetImageWithFadeAnimation // 淡入动画
                     progress:nil
                    transform:^UIImage * _Nullable(UIImage * _Nonnull image, NSURL * _Nonnull url) {
                        // 调整大小，切圆角
                        image = [image imageByResizeToSize:CGSizeMake(72, 56) contentMode:UIViewContentModeScaleAspectFill];
                        return [image imageByRoundCornerRadius:3];
                   }
                   completion:^(UIImage * _Nullable image, NSURL * _Nonnull url, YYWebImageFromType from, YYWebImageStage stage, NSError * _Nullable error) {
                       if (from == YYWebImageFromDiskCache) {
                           BLYLogDebug(@"load image from disk cache");
                       }
                   }];
```



### 图片缓存

```objectivec
YYImageCache *cache = [YYWebImageManager sharedManager].cache;
    
// 获取缓存大小
cache.memoryCache.totalCost;
cache.memoryCache.totalCount;
cache.diskCache.totalCost;
cache.diskCache.totalCount;
    
// 清空缓存
[cache.memoryCache removeAllObjects];
[cache.diskCache removeAllObjects];
    
// 清空磁盘缓存，带进度回调
[cache.diskCache removeAllObjectsWithProgressBlock:^(int removedCount, int totalCount) {
   // progress
} endBlock:^(BOOL error) {
   // end
}];
```



### 用颜色生成图片

```objectivec
+ (nullable UIImage *)yy_imageWithColor:(UIColor *)color;
+ (nullable UIImage *)yy_imageWithColor:(UIColor *)color size:(CGSize)size;
```



安装
==============

### CocoaPods

1. 将 cocoapods 更新至最新版本.
2. 在 Podfile 中添加 `pod 'YYWebImage'`。
3. 执行 `pod install` 或 `pod update`。
4. 导入 \<YYWebImage/YYWebImage.h\>。
5. 注意：pod 配置并没有包含 WebP 组件, 如果你需要支持 WebP，可以在 Podfile 中添加 `pod 'YYImage/WebP'`。你可以调用 `YYImageWebPAvailable()` 来检查一下 WebP 组件是否被正确安装。

### Carthage

1. 在 Cartfile 中添加 `github "ibireme/YYWebImage"`。
2. 执行 `carthage update --platform ios` 并将生成的 framework 添加到你的工程。
3. 导入 \<YYWebImage/YYWebImage.h\>。
4. 注意: carthage framework 并没有包含 webp 组件。如果你需要支持 WebP，可以用 CocoaPods 安装，或者手动安装。

### 手动安装

1. 下载 YYWebImage 文件夹内的所有内容。
2. 将 YYWebImage 内的源文件添加(拖放)到你的工程。
3. 链接以下 frameworks:
* UIKit
* CoreFoundation
* QuartzCore
* AssetsLibrary
* ImageIO
* Accelerate
* MobileCoreServices
* sqlite3
* libz
4. 导入 `YYWebImage.h`。
5. 注意：如果你需要支持 WebP，可以将 `Vendor/WebP.framework`(静态库) 加入你的工程。你可以调用 `YYImageWebPAvailable()` 来检查一下 WebP 组件是否被正确安装。


文档
==============
你可以在 [CocoaDocs](http://cocoadocs.org/docsets/YYWebImage/) 查看在线 API 文档，也可以用 [appledoc](https://github.com/tomaz/appledoc) 本地生成文档。


系统要求
==============
该项目最低支持 `iOS 6.0` 和 `Xcode 8.0`。


许可证
==============
YYWebImage 使用 MIT 许可证，详情见 LICENSE 文件。

相关链接
==============
[移动端图片格式调研](http://blog.ibireme.com/2015/11/02/mobile_image_benchmark/)

[iOS 处理图片的一些小 Tip](http://blog.ibireme.com/2015/11/02/ios_image_tips/)


