![](http://upload-images.jianshu.io/upload_images/2648731-0490351d8bdfb3bb.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)
AFNetworking 是一个适用于 iOS、macOS、watchOS 和 tvOS 系统的网络框架。它建立在 [Foundation URL Loading System](http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/URLLoadingSystem/URLLoadingSystem.html) 之上，扩展了 Cocoa 强大的高级网络抽象功能。设计精良的模块化架构、功能丰富的 API，让你能够安心的使用，轻松实现各种网络请求，比如 GET 请求，POST 请求。



## 如何开始

- [Download AFNetworking](https://github.com/AFNetworking/AFNetworking/archive/master.zip) and try out the included Mac and iPhone example apps
- Read the ["Getting Started" guide](https://github.com/AFNetworking/AFNetworking/wiki/Getting-Started-with-AFNetworking), [FAQ](https://github.com/AFNetworking/AFNetworking/wiki/AFNetworking-FAQ), or [other articles on the Wiki](https://github.com/AFNetworking/AFNetworking/wiki)
- Check out the [documentation](http://cocoadocs.org/docsets/AFNetworking/) for a comprehensive look at all of the APIs available in AFNetworking
- Read the [AFNetworking 3.0 Migration Guide](https://github.com/AFNetworking/AFNetworking/wiki/AFNetworking-3.0-Migration-Guide) for an overview of the architectural changes from 2.0.



## 讨论

- If you **need help**, use [Stack Overflow](http://stackoverflow.com/questions/tagged/afnetworking). (Tag 'afnetworking')
- If you'd like to **ask a general question**, use [Stack Overflow](http://stackoverflow.com/questions/tagged/afnetworking).
- If you **found a bug**, _and can provide steps to reliably reproduce it_, open an issue.
- If you **have a feature request**, open an issue.
- If you **want to contribute**, submit a pull request.



## 安装
AFNetworking 支持多种安装方法。


### CocoaPods 安装

[CocoaPods](http://cocoapods.org) is a dependency manager for Objective-C, which automates and simplifies the process of using 3rd-party libraries like AFNetworking in your projects. See the ["Getting Started" guide for more information](https://github.com/AFNetworking/AFNetworking/wiki/Getting-Started-with-AFNetworking). You can install it with the following command:

```bash
$ gem install cocoapods
```

> CocoaPods 0.39.0+ is required to build AFNetworking 3.0.0+.

#### 配置

To integrate AFNetworking into your Xcode project using CocoaPods, specify it in your `Podfile`:

```ruby
pod 'AFNetworking', '~> 4.0'
```

Then, run the following command:

```bash
$ pod install
```

### Carthage 安装

[Carthage](https://github.com/Carthage/Carthage) is a decentralized dependency manager that builds your dependencies and provides you with binary frameworks.

You can install Carthage with [Homebrew](http://brew.sh/) using the following command:

```bash
$ brew update
$ brew install carthage
```

To integrate AFNetworking into your Xcode project using Carthage, specify it in your `Cartfile`:

```ogdl
github "AFNetworking/AFNetworking" ~> 3.0
```

Run `carthage` to build the framework and drag the built `AFNetworking.framework` into your Xcode project.



## 版本要求

| AFNetworking Version | Minimum iOS Target | Minimum OS X Target | Minimum watchOS Target | Minimum tvOS Target |                            Notes                             |
| :------------------: | :----------------: | :-----------------: | :--------------------: | :-----------------: | :----------------------------------------------------------: |
|         4.x          |       iOS 9        |     macOS 10.10     |      watchOS 2.0       |      tvOS 9.0       |                    Xcode 11+ is required.                    |
|         3.x          |       iOS 7        |      OS X 10.9      |      watchOS 2.0       |      tvOS 9.0       | Xcode 7+ is required. `NSURLConnectionOperation` support has been removed. |
|     2.6 -> 2.6.3     |       iOS 7        |      OS X 10.9      |      watchOS 2.0       |         n/a         |                    Xcode 7+ is required.                     |
|     2.0 -> 2.5.4     |       iOS 6        |      OS X 10.8      |          n/a           |         n/a         | Xcode 5+ is required. `NSURLSession` subspec requires iOS 7 or OS X 10.9. |
|         1.x          |       iOS 5        |    Mac OS X 10.7    |          n/a           |         n/a         |                                                              |
|        0.10.x        |       iOS 4        |    Mac OS X 10.6    |          n/a           |         n/a         |                                                              |

(OS X projects must support [64-bit with modern Cocoa runtime](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtVersionsPlatforms.html)).

> Programming in Swift? Try [Alamofire](https://github.com/Alamofire/Alamofire) for a more conventional set of APIs.



## 架构

### NSURLSession

- `AFURLSessionManager`
- `AFHTTPSessionManager`

### 序列化

* `<AFURLRequestSerialization>` 请求序列化
  - `AFHTTPRequestSerializer`
  - `AFJSONRequestSerializer`
  - `AFPropertyListRequestSerializer`
* `<AFURLResponseSerialization>` 响应序列化
  - `AFHTTPResponseSerializer`
  - `AFJSONResponseSerializer`
  - `AFXMLParserResponseSerializer`
  - `AFXMLDocumentResponseSerializer` _(Mac OS X)_
  - `AFPropertyListResponseSerializer`
  - `AFImageResponseSerializer`
  - `AFCompoundResponseSerializer`

### 其他功能

- `AFSecurityPolicy` 安全策略模块
- `AFNetworkReachabilityManager` 网络状态检测模块



### 项目模块

![006tKfTcly1fj0ndu06qaj307f0bqmye.jpg](http://upload-images.jianshu.io/upload_images/2648731-4ebc14b907108a87.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



## 用法

### 一、AFURLSessionManager 

* `AFURLSessionManager` 创建并管理 `NSURLSession` 对象。
* `NSURLSession` 对象的创建需要基于指定的 `NSURLSessionConfiguration` 对象创建。
* `NSURLSessionConfiguration` 对象遵守如下协议：
  * `<NSURLSessionDelegate>` 
  * `<NSURLSessionTaskDelegate>`
  * `<NSURLSessionDataDelegate>`
  * `<NSURLSessionDownloadDelegate>`
  



#### NSURLSessionTask

![](http://upload-images.jianshu.io/upload_images/2648731-fe67c8e448c7ce34.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

#### NSURLSessionDelegate 委托协议

![](http://upload-images.jianshu.io/upload_images/2648731-23944504e81ef45f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



#### 1. 创建下载任务

该类中提供了创建两种下载任务的方法，一种是一次性下载任务，另一种是可恢复下载任务，即拿着上一次下载了一部分的缓存数据继续下载。

```objectivec
// 1.创建 NSURLSessionConfiguration 对象
NSURLSessionConfiguration *configuration = [NSURLSessionConfiguration defaultSessionConfiguration];

// 2.创建 AFURLSessionManager 对象
AFURLSessionManager *manager = [[AFURLSessionManager alloc] initWithSessionConfiguration:configuration];

// 3.创建 NSURL、NSURLRequest 对象
NSURL *URL = [NSURL URLWithString:@"http://example.com/download.zip"];
NSURLRequest *request = [NSURLRequest requestWithURL:URL];

// 4.创建 NSURLSessionDownloadTask 对象
NSURLSessionDownloadTask *downloadTask = [manager downloadTaskWithRequest:request progress:nil destination:^NSURL *(NSURL *targetPath, NSURLResponse *response) {
    NSURL *documentsDirectoryURL = [[NSFileManager defaultManager] URLForDirectory:NSDocumentDirectory inDomain:NSUserDomainMask appropriateForURL:nil create:NO error:nil];
    // 下载数据的目标路径
    return [documentsDirectoryURL URLByAppendingPathComponent:[response suggestedFilename]];
} completionHandler:^(NSURLResponse *response, NSURL *filePath, NSError *error) {
    // 下载完成后调用
    NSLog(@"File downloaded to: %@", filePath);
}];
// 5.开启下载任务
[downloadTask resume];
```

#### 2. 创建上传任务

该类中提供了创建三种上传任务的方法，主要以数据的上传形式进行区分：Data 数据、File 数据、Stream 流式数据。

```objectivec
// 1.创建 NSURLSessionConfiguration 对象
NSURLSessionConfiguration *configuration = [NSURLSessionConfiguration defaultSessionConfiguration];

// 2.创建 AFURLSessionManager 对象
AFURLSessionManager *manager = [[AFURLSessionManager alloc] initWithSessionConfiguration:configuration];

// 3.创建 NSURL、NSURLRequest 对象
NSURL *URL = [NSURL URLWithString:@"http://example.com/upload"];
NSURLRequest *request = [NSURLRequest requestWithURL:URL];

// 4.创建 NSURLSessionUploadTask 对象
NSURL *filePath = [NSURL fileURLWithPath:@"file://path/to/image.png"];
NSURLSessionUploadTask *uploadTask = [manager uploadTaskWithRequest:request fromFile:filePath progress:nil completionHandler:^(NSURLResponse *response, id responseObject, NSError *error) {
    if (error) {
        NSLog(@"Error: %@", error);
    } else {
        NSLog(@"Success: %@ %@", response, responseObject);
    }
}];
// 5.开启上传任务
[uploadTask resume];
```

#### 3. 创建上传任务，包含多个 Request 信息，带上传进度指示条

```objectivec
// 1.创建 NSMutableURLRequest 对象，并设置一系列参数
NSMutableURLRequest *request = [[AFHTTPRequestSerializer serializer] multipartFormRequestWithMethod:@"POST" URLString:@"http://example.com/upload" parameters:nil constructingBodyWithBlock:^(id<AFMultipartFormData> formData) {
    [formData appendPartWithFileURL:[NSURL fileURLWithPath:@"file://path/to/image.jpg"] name:@"file" fileName:@"filename.jpg" mimeType:@"image/jpeg" error:nil];
} error:nil];

// 2.创建 AFURLSessionManager 对象
AFURLSessionManager *manager = [[AFURLSessionManager alloc] initWithSessionConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]];

// 3.创建 NSURLSessionUploadTask 对象
NSURLSessionUploadTask *uploadTask;
uploadTask = [manager
              uploadTaskWithStreamedRequest:request
              progress:^(NSProgress * _Nonnull uploadProgress) {
                  // 此处不会在主线程上回调
                  // 你要负责在主线程上更新 UI
                  dispatch_async(dispatch_get_main_queue(), ^{
                      // Update the progress view
                      [progressView setProgress:uploadProgress.fractionCompleted];
                  });
              }
              completionHandler:^(NSURLResponse * _Nonnull response, id  _Nullable responseObject, NSError * _Nullable error) {
                  if (error) {
                      NSLog(@"Error: %@", error);
                  } else {
                      NSLog(@"%@ %@", response, responseObject);
                  }
              }];
// 4.开启下载任务
[uploadTask resume];
```

#### 4.创建数据任务

```objectivec
// 1.创建 NSURLSessionConfiguration 对象
NSURLSessionConfiguration *configuration = [NSURLSessionConfiguration defaultSessionConfiguration];

// 2.创建 AFURLSessionManager 对象
AFURLSessionManager *manager = [[AFURLSessionManager alloc] initWithSessionConfiguration:configuration];

// 3.创建 NSURL、NSURLRequest 对象
NSURL *URL = [NSURL URLWithString:@"http://httpbin.org/get"];
NSURLRequest *request = [NSURLRequest requestWithURL:URL];


// 4.创建 NSURLSessionDataTask 对象
NSURLSessionDataTask *dataTask = [manager dataTaskWithRequest:request
                                               uploadProgress:^(NSProgress * _Nonnull uploadProgress) {

} downloadProgress:^(NSProgress * _Nonnull downloadProgress) {

} completionHandler:^(NSURLResponse * _Nonnull response, id  _Nullable responseObject, NSError * _Nullable error) {
    if (error) {
        NSLog(@"Error: %@", error);
    } else {
        NSLog(@"%@ %@", response, responseObject);
    }
}];

// 5.开启下载任务
[dataTask resume];
```



从使用上看，`AFURLSessionManager` 只是封装了原生的 `NSURLSession` 对象，创建网络请求步骤流程与原生方法（使用 `NSURLSession` 类）类似：

![AFURLSessionManager](http://upload-images.jianshu.io/upload_images/2648731-136ae5b20e9b7c55.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)





### 二、AFHTTPSessionManager

`AFHTTPSessionManager` 是 `AFURLSessionManager` 的子类对象，它提供了进行 HTTP 请求的便捷方法。当设置好 `baseURL` （服务器地址）时，要发起 `GET`、`POST` 等请求时，你就可以使用本类中的便捷方法了，而这些便捷方法只需要传入相对路径参数即可。

总之，`AFHTTPSessionManager` 提供了发起 `GET`、`HEAD`、`POST`、`PUT`、`PATCH`、`DELETE` 请求的便捷语法。它调用的是父类 `AFURLSessionManager` 创建的 `NSURLSessionDataTask` 类型的任务。

这个类并没有实现像 `NSURLSessionUploadTask`、`NSURLSessionDownloadTask`、`NSURLSessionStreamTask` 类型的任务。也就是说，你不能通过这个类创建上传任务、下载任务或者 `Stream` 流式任务！

鼓励以 iOS 7 或 Mac OS X 10.9 或更高版本为目标，并广泛处理 Web 服务的开发人员使用 `AFHTTPSessionManager` 子类，并提供一种类方法，该方法返回共享的单例对象，在该对象上可以在应用程序之间共享身份验证和其他配置。

通过 HTTP 客户端创建的请求，将按照 `requestSerializer` 属性的设置包含默认的请求头和编码参数。 `requestSerializer` 属性的实例是一个遵守 `AFURLRequestSerialization` 协议的对象。

从服务器接收到的响应数据将按照 `responseSerializers` 属性的设置自动验证并且序列化。 `responseSerializers` 属性的实例是一个遵守 `AFURLResponseSerialization` 协议的对象



初始化方法：

```objectivec
+ (instancetype)manager;
- (instancetype)initWithBaseURL:(nullable NSURL *)url;
- (instancetype)initWithBaseURL:(nullable NSURL *)url
           sessionConfiguration:(nullable NSURLSessionConfiguration *)configuration;
```

发起 HTTP 请求方法：

```objectivec
// GET
- (nullable NSURLSessionDataTask *)GET:(NSString *)URLString
                            parameters:(nullable id)parameters
                               headers:(nullable NSDictionary <NSString *, NSString *> *)headers
                              progress:(nullable void (^)(NSProgress *downloadProgress))downloadProgress
                               success:(nullable void (^)(NSURLSessionDataTask *task, id _Nullable responseObject))success
                               failure:(nullable void (^)(NSURLSessionDataTask * _Nullable task, NSError *error))failure;

// HEAD
- (nullable NSURLSessionDataTask *)HEAD:(NSString *)URLString
                             parameters:(nullable id)parameters
                                headers:(nullable NSDictionary <NSString *, NSString *> *)headers
                                success:(nullable void (^)(NSURLSessionDataTask *task))success
                                failure:(nullable void (^)(NSURLSessionDataTask * _Nullable task, NSError *error))failure;

// POST
- (nullable NSURLSessionDataTask *)POST:(NSString *)URLString
                             parameters:(nullable id)parameters
                                headers:(nullable NSDictionary <NSString *, NSString *> *)headers
                               progress:(nullable void (^)(NSProgress *uploadProgress))uploadProgress
                                success:(nullable void (^)(NSURLSessionDataTask *task, id _Nullable responseObject))success
                                failure:(nullable void (^)(NSURLSessionDataTask * _Nullable task, NSError *error))failure;

- (nullable NSURLSessionDataTask *)POST:(NSString *)URLString
                             parameters:(nullable id)parameters
                                headers:(nullable NSDictionary <NSString *, NSString *> *)headers
              constructingBodyWithBlock:(nullable void (^)(id <AFMultipartFormData> formData))block
                               progress:(nullable void (^)(NSProgress *uploadProgress))uploadProgress
                                success:(nullable void (^)(NSURLSessionDataTask *task, id _Nullable responseObject))success
                                failure:(nullable void (^)(NSURLSessionDataTask * _Nullable task, NSError *error))failure;

// PUT
- (nullable NSURLSessionDataTask *)PUT:(NSString *)URLString
                            parameters:(nullable id)parameters
                               headers:(nullable NSDictionary <NSString *, NSString *> *)headers
                               success:(nullable void (^)(NSURLSessionDataTask *task, id _Nullable responseObject))success
                               failure:(nullable void (^)(NSURLSessionDataTask * _Nullable task, NSError *error))failure;

// PATCH
- (nullable NSURLSessionDataTask *)PATCH:(NSString *)URLString
                              parameters:(nullable id)parameters
                                 headers:(nullable NSDictionary <NSString *, NSString *> *)headers
                                 success:(nullable void (^)(NSURLSessionDataTask *task, id _Nullable responseObject))success
                                 failure:(nullable void (^)(NSURLSessionDataTask * _Nullable task, NSError *error))failure;

// DELETE
- (nullable NSURLSessionDataTask *)DELETE:(NSString *)URLString
                               parameters:(nullable id)parameters
                                  headers:(nullable NSDictionary <NSString *, NSString *> *)headers
                                  success:(nullable void (^)(NSURLSessionDataTask *task, id _Nullable responseObject))success
                                  failure:(nullable void (^)(NSURLSessionDataTask * _Nullable task, NSError *error))failure;

// 创建自定义请求方法
- (nullable NSURLSessionDataTask *)dataTaskWithHTTPMethod:(NSString *)method
                                                URLString:(NSString *)URLString
                                               parameters:(nullable id)parameters
                                                  headers:(nullable NSDictionary <NSString *, NSString *> *)headers
                                           uploadProgress:(nullable void (^)(NSProgress *uploadProgress))uploadProgress
                                         downloadProgress:(nullable void (^)(NSProgress *downloadProgress))downloadProgress
                                                  success:(nullable void (^)(NSURLSessionDataTask *task, id _Nullable responseObject))success
                                                  failure:(nullable void (^)(NSURLSessionDataTask * _Nullable task, NSError *error))failure;
```

#### 一、GET 请求

```objectivec
NSURL *baseURL = [NSURL URLWithString:@"http://example.com/v1/"];
// 默认会话配置
NSURLSessionConfiguration *defaultConfig = [NSURLSessionConfiguration defaultSessionConfiguration];

// 1.创建 AFHTTPSessionManager 对象
AFHTTPSessionManager *manager = [[AFHTTPSessionManager alloc] initWithBaseURL:baseURL sessionConfiguration:defaultConfig];

// 2.构建参数，parameters 参数可以传 nil
NSString *urlString = @"/login";

// 3.Task 任务
[manager GET:urlString parameters:nil headers:nil
    progress:^(NSProgress * _Nonnull downloadProgress) {
    // 请求进度
} success:^(NSURLSessionDataTask * _Nonnull task, id  _Nullable responseObject) {
    // 请求成功
    // task: 通过 task 拿到响应头
    // responseObject: 请求成功返回的响应结果（AFN内部已经把响应体转换为OC对象，通常是字典或数组)
} failure:^(NSURLSessionDataTask * _Nullable task, NSError * _Nonnull error) {
    // 请求失败
    NSLog(@"GET ERROR:%@",error.localizedDescription);
}];
```

#### 二、POST 请求

```objectivec
// 1.创建 AFHTTPSessionManager 对象
AFHTTPSessionManager *manager = [[AFHTTPSessionManager alloc]
    initWithBaseURL:[NSURL URLWithString:@"https://www.google.com"]];

// 2.请求接口
NSString *relativePaths = @"/login";
// 2.请求参数
NSDictionary *parameters = @{
                             @"username":@"admin",
                             @"password":@"123456",
                             };
// 3.Task 任务
[manager POST:relativePaths parameters:parameters headers:nil progress:^(NSProgress * _Nonnull uploadProgress) {
    // 进度
} success:^(NSURLSessionDataTask * _Nonnull task, id  _Nullable responseObject) {
    // 请求成功
} failure:^(NSURLSessionDataTask * _Nullable task, NSError * _Nonnull error) {
    // 请求失败
}];
```

### 三、其他设置

#### `timeoutInterval` 设置请求超时时间，默认为 60s：

```objectivec
// 设置请求超时时间为 10s
[manager.requestSerializer willChangeValueForKey:@"timeoutInterval"];
manager.requestSerializer.timeoutInterval = 10.f;
[manager.requestSerializer didChangeValueForKey:@"timeoutInterval"];
```


#### ContentTypes 类型

```objectivec
// AFN 默认接收的 ContentTypes 有以下三种：
self.acceptableContentTypes = [NSSet setWithObjects:@"application/json", @"text/json", @"text/javascript", nil];

// 如果服务器返回的 ContentTypes 类型为 text/html，需要设置如下：
manager.responseSerializer.acceptableContentTypes = [NSSet setWithObject:@"text/html"];
```

#### `responseSerializer` 默认把服务器返回的数据当做是 JSON 类型并自动转化为 Objective-C

```objectivec
// 设置为 XML 类型，此时返回的是 NSXMLParser 类型，需要我们自己解析。
manager.responseSerializer = [AFXMLParserResponseSerializer serializer];

// 设置为其他类型
manager.responseSerializer = [AFHTTPResponseSerializer serializer];
```

#### 设置安全策略，验证 SSL 证书

```objectivec
// 验证 CN 域名
NSString *cerPath = [[NSBundle mainBundle] pathForResource:@"server" ofType:@"cer"];
NSData *cerData = [NSData dataWithContentsOfFile:cerPath];
manager.securityPolicy = [AFSecurityPolicy policyWithPinningMode:AFSSLPinningModeCertificate withPinnedCertificates:[[NSSet alloc]initWithObjects:cerData, nil]];

// 是否信任具有无效或过期 SSL 证书的服务器（自建证书）
manager.securityPolicy.allowInvalidCertificates = YES;

// 是否在证书的 CN 字段中验证域名
manager.securityPolicy.validatesDomainName = NO;
```

#### 设置状态栏活动指示器

启用后，系统会自动在应用程序发起网络请求时，在状态栏显示旋转的 loading 动画。

1. 在项目的 `AppDelegate.m` 文件中导入` AFNetworkActivityIndicatorManager.h` 文件；
2. 在`AppDelegate application:didFinishLaunchingWithOptions:`方法中设置如下代码：

```objectivec
[[AFNetworkActivityIndicatorManager sharedManager] setEnabled:YES];
```



### 四、请求序列化

请求序列化通过将请求参数编码为查询字符串或者 HTTP 请求体，来为 URL 字符串创建请求。

```objectivec
NSString *URLString = @"http://example.com";
NSDictionary *parameters = @{@"foo": @"bar", @"baz": @[@1, @2, @3]};
```

#### 一、编码为查询字符串

```objectivec
// GET
[[AFHTTPRequestSerializer serializer] requestWithMethod:@"GET" URLString:URLString parameters:parameters error:nil];
```

```bash
GET http://example.com?foo=bar&baz[]=1&baz[]=2&baz[]=3
```

#### 二、编码为URL表单

```objectivec
// POST
[[AFHTTPRequestSerializer serializer] requestWithMethod:@"POST" URLString:URLString parameters:parameters error:nil];
```

```bash
POST http://example.com/
Content-Type: application/x-www-form-urlencoded

foo=bar&baz[]=1&baz[]=2&baz[]=3
```

#### 三、编码为 JSON 字符串

```objectivec
// AFJSONRequestSerializer
[[AFJSONRequestSerializer serializer] requestWithMethod:@"POST" URLString:URLString parameters:parameters error:nil];
```

```bash
POST http://example.com/
Content-Type: application/json

{"foo": "bar", "baz": [1,2,3]}
```



### 五、网络状态管理

`AFNetworkReachabilityManager` 类用于监测 WWAN 和 WiFi 接口的网络联通性。

- 不要依据它来决定是否发起网络请求。
  - 你应该尝试发送网络请求。
- 你可以依据它来选择是否自动重新发送请求。
  - 虽然它可能仍然失败，但当连接可用时，可访问性通知是重试某些东西的好时机。
- 它是一个判断网络请求失败原因的很好用的工具。
  - 当网络请求失败时,告诉用户他们已经离线,要比告诉他们一些比如”请求超时”一类的准确的错误更好。

See also [WWDC 2012 session 706, "Networking Best Practices."](https://developer.apple.com/videos/play/wwdc2012-706/).

网络状态管理使用示例：

```objectivec
AFNetworkReachabilityManager *manager = [AFNetworkReachabilityManager sharedManager];
[manager setReachabilityStatusChangeBlock:^(AFNetworkReachabilityStatus status) {
    switch (status) {
        case AFNetworkReachabilityStatusUnknown: {
            DDLogInfo(@"网络异常：未知网络");
            break;
        }
        case AFNetworkReachabilityStatusNotReachable: {
            DDLogInfo(@"网络异常：没有网络");
            break;
        }
        case AFNetworkReachabilityStatusReachableViaWWAN: {
            DDLogInfo(@"网络状态检测：蜂窝网络");
            break;
        }
        case AFNetworkReachabilityStatusReachableViaWiFi: {
            DDLogInfo(@"网络状态检测：WiFi");
            break;                
        }
    }
}];
[networkReachabilityManager startMonitoring]; // 开启网络检测
```



### 六、安全策略

`AFSecurityPolicy` 基于 X.509 数字证书和公钥来确定服务器是否可信。

向应用程序中添加固定的 SSL 证书有助于防止中间人攻击和其他漏洞。强烈建议处理敏感客户数据或财务信息的应用程序通过 HTTPS 的连接方式和服务器通信。

#### 允许无效的 SSL 证书

```objectivec
AFHTTPSessionManager *manager = [AFHTTPSessionManager manager];
manager.securityPolicy.allowInvalidCertificates = YES; // 生产环境不建议使用
```

---

## 单元测试

AFNetworking 在 Tests 子目录中包含一套单元测试。 这些测试可以简单地执行您想要测试的平台框架上的测试操作。

## 账户

AFNetworking is owned and maintained by the [Alamofire Software Foundation](http://alamofire.org).

AFNetworking was originally created by [Scott Raymond](https://github.com/sco/) and [Mattt Thompson](https://github.com/mattt/) in the development of [Gowalla for iPhone](http://en.wikipedia.org/wiki/Gowalla).

AFNetworking's logo was designed by [Alan Defibaugh](http://www.alandefibaugh.com/).

And most of all, thanks to AFNetworking's [growing list of contributors](https://github.com/AFNetworking/AFNetworking/contributors).

### Security Disclosure

If you believe you have identified a security vulnerability with AFNetworking, you should report it as soon as possible via email to security@alamofire.org. Please do not post it to a public issue tracker.

## License

AFNetworking is released under the MIT license. See LICENSE for details.
