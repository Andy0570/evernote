参考：[SDWebImage/Core/SDWebImageDefine.h](https://github.com/SDWebImage/SDWebImage/blob/master/SDWebImage/Core/SDWebImageDefine.h)


### `SDWebImageRetryFailed`，图像下载失败后，仍旧继续尝试下载

默认情况下，当一个 URL 无法下载，或者下载失败时，该 URL 将被列入黑名单，因此库不会继续尝试。 

此标志可以禁用黑名单。


### `SDWebImageLowPriority`，低优先级下载

默认情况下，图像下载是在 UI 交互期间开始的，此标志禁用此特性。

例如导致 `UIScrollView` 减速上的下载延迟。



### `SDWebImageProgressiveLoad`，渐进式下载

默认情况下，图像仅在完全下载后才会显示。该标志启用渐进式下载，图像在下载过程中按浏览器的方式逐行显示。



### `SDWebImageRefreshCached`，刷新缓存

即使图片被缓存了，也请遵守 HTTP 响应缓存控制，并在需要时从远程位置刷新缓存。磁盘缓存将由 `NSURLCache` 而不是 `SDWebImage` 处理，从而导致性能略有下降。

此选项有助于处理相同请求网址后更改的图像，例如 Facebook API 个人资料图片。如果刷新了缓存的图像，则对缓存的图像调用一次完成块，然后对最终图像调用一次。



### `SDWebImageContinueInBackground`，后台继续下载

在 iOS 4 以上版本中，如果应用程序进入后台运行，则继续下载图像。这是通过要求系统提供在后台增加时间以使请求完成。如果后台任务到期，则该操作将被取消。



### `SDWebImageHandleCookies`，处理 cookie 缓存

通过设置 `NSMutableURLRequest.HTTPShouldHandleCookies = YES;` 处理存储在 `NSHTTPCookieStorage` 中的 cookie 缓存。



### `SDWebImageAllowInvalidSSLCertificates`，允许非法的 HTTPS 链接

启用以允许不受信任的 SSL 证书。

该选项仅用于测试目的。在生产中请谨慎使用。



### `SDWebImageHighPriority`，高优先级下载

默认情况下，图像按排队顺序加载。此标志将它们移至队列的最前面。



### `SDWebImageDelayPlaceholder`，延迟加载占位图片

默认情况下，占位图会在下载图片时加载显示。该标志将延迟加载占位图，直到图像下载完成为止。



### `SDWebImageTransformAnimatedImage`，对动图进行转换

通常情况下，我们不会对动画图像应用变换，因为大多数转换无法正确管理动画图像。

使用此标志以始终对其进行转换。



### `SDWebImageAvoidAutoSetImage`，避免自动设置图像

默认情况下，图像下载后将被添加到 imageView 中。但是在某些情况下，你可能需要在设置图像之前进行预处理（例如，应用滤镜或将其与淡入淡出动画一起添加）如果需要在下载成功时手动设置图像，请使用此标志。



### `SDWebImageScaleDownLargeImages`，自动缩放下载图像

默认情况下，图像会根据其原始大小进行解码。此标志会将图像缩小到与设备的受限内存兼容的大小。要控制限制内存字节，请检查 `SDImageCoderHelper.defaultScaleDownLimitBytes`（在iOS上默认为 60 MB）

这实际上将转换为使用 v5.5.0 中的上下文选项 `.imageThumbnailPixelSize`（在iOS上默认为 `(3966, 3966)` ）。以前没有。

从 v5.5.0 起，此标志也会影响渐进式和动画图像。以前没有。

如果需要使用细节控件，最好使用上下文选项 `imageThumbnailPixelSize` 和 `imagePreserveAspectRatio`。



### `SDWebImageQueryMemoryData`，异步查询图像 data 数据

默认情况下，当图像已经缓存在内存中时，我们不查询图像数据。 此掩码可以强制同时查询图像数据。 但是，除非您通过  `SDWebImageQueryMemoryDataSync` 枚举指定同步查询，否则该查询是异步的。



### `SDWebImageQueryDiskDataSync`，同步查询图像 data 数据

默认情况下，当您仅指定 `SDWebImageQueryMemoryData` 时，我们将异步查询图片的内存数据。 还结合了此掩码以同步查询内存图像数据。

不建议同步查询数据，除非您要确保将映像加载到同一运行循环中，以避免在单元重用期间闪烁。



### `SDWebImageFromLoaderOnly`，不使用缓存，仅加载网络图像

默认情况下，通过加载器加载图片之前会先查询缓存。 该标志可以防止这样做，仅加载网络图像。



### `SDWebImageForceTransition`，强制转换图片

默认情况下，在图像加载完成后，当您使用 `SDWebImageTransition` 进行某些视图转换时，此转换仅适用于从网络下载图像。 此掩码也可以强制将视图转换应用于内存和磁盘缓存。



### `SDWebImageAvoidDecodeImage`，防止对图像进行解码

默认情况下，我们将在缓存查询和从网络下载期间在后台解码图像。 这有助于提高性能，因为在屏幕上渲染图像时，首先需要对其进行解码。 但这是由 Core Animation 在主队列上发生的。

但是，此过程也可能会增加内存使用率。 如果由于过多的内存消耗而遇到问题，则此标志可以防止对图像进行解码。



### `SDWebImageDecodeFirstFrameOnly`，生成动图的第一帧并作为静态图片

默认情况下，我们会对动画图像进行解码。 该标志可以仅强制解码第一帧并生成静态图像。



### `SDWebImagePreloadAllFrames`，预先加载动图

默认情况下，对于 `SDAnimatedImage` 图片，只会**在渲染过程中**对动画图像帧进行解码以减少内存使用量。 但是，当动画图像由许多 `imageViews` 共享时，您可以指定将所有帧预加载到内存中，以减少 CPU 使用率。

这实际上会在后台队列中触发 `preloadAllAnimatedImageFrames`（仅限磁盘缓存和下载）。



### `SDWebImageMatchAnimatedImageClass`，为动图设置备用图片方案？

默认情况下，当您使用 `SDWebImageContextAnimatedImageClass` 上下文选项（例如使用设计为使用 `SDAnimatedImage` 的 `SDAnimatedImageView`）时，当内存缓存命中或图像解码器无法产生一个完全匹配时，我们仍然可以使用 `UIImage`。 您的自定义类作为后备解决方案。

使用此选项，可以确保我们始终使用您提供的类回调图像。 如果无法生成一个，将使用代码 `SDWebImageErrorBadImageData` 的错误。

注意，此选项与总是生成 `UIImage` / `NSImage` 的 `SDWebImageDecodeFirstFrameOnly`  选项不兼容。



### `SDWebImageWaitStoreCache`，等待缓存完成后再处理图像

默认情况下，当我们从网络加载图像时，图像将被写入缓存（内存和磁盘，由您的 `storeCacheType` 上下文选项控制）。

这可能是异步操作，并且最终的 `SDInternalCompletionBlock` 回调不保证写入的磁盘缓存已完成，并且可能导致逻辑错误。 （例如，您仅在完成块中修改了磁盘数据，但是磁盘缓存尚未准备好）

如果需要在完成块中处理磁盘缓存，则应使用此选项以确保在回调时已写入磁盘缓存。

请注意，如果在使用自定义缓存序列化程序或转换器时使用了此选项，我们还将等待直到写入的输出图像数据完成为止。



### `SDWebImageTransformVectorImage`，转换矢量图像

我们通常不对矢量图像应用变换，因为矢量图像支持动态更改为任意大小，栅格化为固定大小会丢失细节。 要修改矢量图像，可以在运行时处理矢量数据（例如修改 PDF 标签 / SVG 元素）。

无论如何，请使用此标志对其进行转换。



## 源码

```objc
/// WebCache options
 typedef NS_OPTIONS(NSUInteger, SDWebImageOptions) {
     /**
      * By default, when a URL fail to be downloaded, the URL is blacklisted so the library won't keep trying.
      * This flag disable this blacklisting.
      */
     SDWebImageRetryFailed = 1 << 0,
     
     /**
      * By default, image downloads are started during UI interactions, this flags disable this feature,
      * leading to delayed download on UIScrollView deceleration for instance.
      */
     SDWebImageLowPriority = 1 << 1,
     
     /**
      * This flag enables progressive download, the image is displayed progressively during download as a browser would do.
      * By default, the image is only displayed once completely downloaded.
      */
     SDWebImageProgressiveLoad = 1 << 2,
     
     /**
      * Even if the image is cached, respect the HTTP response cache control, and refresh the image from remote location if needed.
      * The disk caching will be handled by NSURLCache instead of SDWebImage leading to slight performance degradation.
      * This option helps deal with images changing behind the same request URL, e.g. Facebook graph api profile pics.
      * If a cached image is refreshed, the completion block is called once with the cached image and again with the final image.
      *
      * Use this flag only if you can't make your URLs static with embedded cache busting parameter.
      */
     SDWebImageRefreshCached = 1 << 3,
     
     /**
      * In iOS 4+, continue the download of the image if the app goes to background. This is achieved by asking the system for
      * extra time in background to let the request finish. If the background task expires the operation will be cancelled.
      */
     SDWebImageContinueInBackground = 1 << 4,
     
     /**
      * Handles cookies stored in NSHTTPCookieStore by setting
      * NSMutableURLRequest.HTTPShouldHandleCookies = YES;
      */
     SDWebImageHandleCookies = 1 << 5,
     
     /**
      * Enable to allow untrusted SSL certificates.
      * Useful for testing purposes. Use with caution in production.
      */
     SDWebImageAllowInvalidSSLCertificates = 1 << 6,
     
     /**
      * By default, images are loaded in the order in which they were queued. This flag moves them to
      * the front of the queue.
      */
     SDWebImageHighPriority = 1 << 7,
     
     /**
      * By default, placeholder images are loaded while the image is loading. This flag will delay the loading
      * of the placeholder image until after the image has finished loading.
      */
     SDWebImageDelayPlaceholder = 1 << 8,
     
     /**
      * We usually don't apply transform on animated images as most transformers could not manage animated images.
      * Use this flag to transform them anyway.
      */
     SDWebImageTransformAnimatedImage = 1 << 9,
     
     /**
      * By default, image is added to the imageView after download. But in some cases, we want to
      * have the hand before setting the image (apply a filter or add it with cross-fade animation for instance)
      * Use this flag if you want to manually set the image in the completion when success
      */
     SDWebImageAvoidAutoSetImage = 1 << 10,
     
     /**
      * By default, images are decoded respecting their original size.
      * This flag will scale down the images to a size compatible with the constrained memory of devices.
      * To control the limit memory bytes, check `SDImageCoderHelper.defaultScaleDownLimitBytes` (Defaults to 60MB on iOS)
      * This will actually translate to use context option `.imageThumbnailPixelSize` from v5.5.0 (Defaults to (3966, 3966) on iOS). Previously does not.
      * This flags effect the progressive and animated images as well from v5.5.0. Previously does not.
      * @note If you need detail controls, it's better to use context option `imageThumbnailPixelSize` and `imagePreserveAspectRatio` instead.
      */
     SDWebImageScaleDownLargeImages = 1 << 11,
     
     /**
      * By default, we do not query image data when the image is already cached in memory. This mask can force to query image data at the same time. However, this query is asynchronously unless you specify `SDWebImageQueryMemoryDataSync`
      */
     SDWebImageQueryMemoryData = 1 << 12,
     
     /**
      * By default, when you only specify `SDWebImageQueryMemoryData`, we query the memory image data asynchronously. Combined this mask as well to query the memory image data synchronously.
      * @note Query data synchronously is not recommend, unless you want to ensure the image is loaded in the same runloop to avoid flashing during cell reusing.
      */
     SDWebImageQueryMemoryDataSync = 1 << 13,
     
     /**
      * By default, when the memory cache miss, we query the disk cache asynchronously. This mask can force to query disk cache (when memory cache miss) synchronously.
      * @note These 3 query options can be combined together. For the full list about these masks combination, see wiki page.
      * @note Query data synchronously is not recommend, unless you want to ensure the image is loaded in the same runloop to avoid flashing during cell reusing.
      */
     SDWebImageQueryDiskDataSync = 1 << 14,
     
     /**
      * By default, when the cache missed, the image is load from the loader. This flag can prevent this to load from cache only.
      */
     SDWebImageFromCacheOnly = 1 << 15,
     
     /**
      * By default, we query the cache before the image is load from the loader. This flag can prevent this to load from loader only.
      */
     SDWebImageFromLoaderOnly = 1 << 16,
     
     /**
      * By default, when you use `SDWebImageTransition` to do some view transition after the image load finished, this transition is only applied for image download from the network. This mask can force to apply view transition for memory and disk cache as well.
      */
     SDWebImageForceTransition = 1 << 17,
     
     /**
      * By default, we will decode the image in the background during cache query and download from the network. This can help to improve performance because when rendering image on the screen, it need to be firstly decoded. But this happen on the main queue by Core Animation.
      * However, this process may increase the memory usage as well. If you are experiencing a issue due to excessive memory consumption, This flag can prevent decode the image.
      */
     SDWebImageAvoidDecodeImage = 1 << 18,
     
     /**
      * By default, we decode the animated image. This flag can force decode the first frame only and produece the static image.
      */
     SDWebImageDecodeFirstFrameOnly = 1 << 19,
     
     /**
      * By default, for `SDAnimatedImage`, we decode the animated image frame during rendering to reduce memory usage. However, you can specify to preload all frames into memory to reduce CPU usage when the animated image is shared by lots of imageViews.
      * This will actually trigger `preloadAllAnimatedImageFrames` in the background queue(Disk Cache & Download only).
      */
     SDWebImagePreloadAllFrames = 1 << 20,
     
     /**
      * By default, when you use `SDWebImageContextAnimatedImageClass` context option (like using `SDAnimatedImageView` which designed to use `SDAnimatedImage`), we may still use `UIImage` when the memory cache hit, or image decoder is not available to produce one exactlly matching your custom class as a fallback solution.
      * Using this option, can ensure we always callback image with your provided class. If failed to produce one, a error with code `SDWebImageErrorBadImageData` will been used.
      * Note this options is not compatible with `SDWebImageDecodeFirstFrameOnly`, which always produce a UIImage/NSImage.
      */
     SDWebImageMatchAnimatedImageClass = 1 << 21,
     
     /**
      * By default, when we load the image from network, the image will be written to the cache (memory and disk, controlled by your `storeCacheType` context option)
      * This maybe an asynchronously operation and the final `SDInternalCompletionBlock` callback does not gurantee the disk cache written is finished and may cause logic error. (For example, you modify the disk data just in completion block, however, the disk cache is not ready)
      * If you need to process with the disk cache in the completion block, you should use this option to ensure the disk cache already been written when callback.
      * Note if you use this when using the custom cache serializer, or using the transformer, we will also wait until the output image data written is finished.
      */
     SDWebImageWaitStoreCache = 1 << 22,
     
     /**
      * We usually don't apply transform on vector images, because vector images supports dynamically changing to any size, rasterize to a fixed size will loss details. To modify vector images, you can process the vector data at runtime (such as modifying PDF tag / SVG element).
      * Use this flag to transform them anyway.
      */
     SDWebImageTransformVectorImage = 1 << 23,
 };
```


