[Bugly 官网](https://bugly.qq.com/v2/index)

> 腾讯Bugly，为移动开发者提供专业的异常上报和运营统计，帮助开发者快速发现并解决异常，同时掌握产品运营动态，及时跟进用户反馈。



最近 iOS 项目中集成了 Bugly ，集成该框架的过程中，90%的问题通过仔细阅读 [Bugly 官方文档](https://bugly.qq.com/docs/user-guide/instruction-manual-ios/?v=20170912151050) 都可以解决。

💡 本文并不会教你如何注册申请 Bugly 帐号，也不会教你如何集成 Bugly SDK，因为官方文档是最权威的，这里只是记录几个注意点（传说中的坑！）。 



## 初始化 Bugly 的注意点 

### 方式一，最简单

在工程`AppDelegate.m`的`application:didFinishLaunchingWithOptions:`方法中初始化：

```objective-c
#import "AppDelegate.h"
#import <Bugly/Bugly.h>

// Bugly 帐号中创建产品后，产品信息中的 AppId
static NSString *const KBuglyAppId = @"**********"; 

@implementation AppDelegate
  
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    [Bugly startWithAppId:KBuglyAppId];
    return YES;
}
```

### 方式二，官方文档中的"高级功能"

Bugly支持读取 `Info.plist` 文件读取SDK初始化参数，可配置的参数如下：

```
- Appid
    - Key: BuglyAppIDString
    - Value: 字符串类型
- 渠道标识
    - Key: BuglyAppChannelString
    - Value: 字符串类型
- 版本信息
    - Key: BuglyAppVersionString
    - Value: 字符串类型
- 开启Debug信息显示
    - Key: BuglyDebugEnable
    - Value: BOOL类型
```

我们设置 `Info.plist` 文件如下：

![image](http://upload-images.jianshu.io/upload_images/2648731-3887c294e671aa44.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如下初始化方式，则会读取`Info.plist`内添加的key-value配置进行SDK初始化：

```objective-c
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // 读取Info.plist中的参数初始化SDK
    [Bugly startWithAppId:nil];
    return YES;
}
```

以上方式初始化 Bugly 确实没问题，可是默认的 `BuglyConfig` 还有几个监控开关没开，我也想顺便开启一下：

```objective-c
// 可能存在问题的代码
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
  
    BuglyConfig *config = [[BuglyConfig alloc] init];
    config.blockMonitorEnable = YES; // 卡顿监控开关，默认关闭
    config.blockMonitorTimeout = 5;
    config.unexpectedTerminatingDetectionEnable = YES; // 非正常退出事件记录开关，默认关闭

    // 读取Info.plist中的参数初始化SDK
    [Bugly startWithAppId:nil config:config];
    
    return YES;
}
```

⚠️ 注意，这样初始化就会有问题，如果配置了`Info.plist`字段，又在初始化时传入了自定义的 `BuglyConfig` 实例，那么在 `Info.plist` 中配置的 `BuglyAppChannelString`、`BuglyAppVersionString`、`BuglyDebugEnable` 会因为被覆盖而失效。

### 方式三，自定义配置

如果需要自定义配置  `BuglyConfig` 实例，这里就不建议不同时配置`Info.plist`字段（或者可以只配置`BuglyAppIDString`字段）：

完整代码：

```objective-c
#import "AppDelegate.h"
#import <Bugly/Bugly.h>

static NSString *const KBuglyAppId = @"**********";

@implementation AppDelegate

#pragma mark - UIApplicationDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    
    [self p_configureForBugly]; 
  
    return YES;
}

#pragma mark - Private

- (void)p_configureForBugly {
    BuglyConfig *config = [[BuglyConfig alloc] init];
    config.channel = @"App Store";
    config.blockMonitorEnable = YES; // 卡顿监控开关，默认关闭
    config.blockMonitorTimeout = 5;
    config.unexpectedTerminatingDetectionEnable = YES; // 非正常退出事件记录开关，默认关闭
    config.delegate = self;

#ifdef DEBUG
    config.debugMode = YES; // debug 模式下，开启调试模式
    config.reportLogLevel = BuglyLogLevelVerbose; // 设置打印日志级别
#else
    config.debugMode = NO; // release 模式下，关闭调试模式
    config.reportLogLevel = BuglyLogLevelWarn; // 设置自定义日志上报的级别，默认不上报自定义日志
#endif
    
    [Bugly startWithAppId:KBuglyAppId config:config];
}
```

⚠️ 如果你配置完 Bugly 之后，

```objective-c
BLYLogError(fmt, ...)
BLYLogWarn(fmt, ...)
BLYLogInfo(fmt, ...)
BLYLogDebug(fmt, ...)
BLYLogVerbose(fmt, ...)
```

如上的日志不会在控制台输出，或者部分类型日志不会输出，那你应该注意到需要设置这段代码：

```
#ifdef DEBUG
    config.debugMode = YES; // debug 模式下，开启调试模式
    config.reportLogLevel = BuglyLogLevelVerbose; // 设置打印日志级别
#else
    config.debugMode = NO; // release 模式下，关闭调试模式
    config.reportLogLevel = BuglyLogLevelWarn; // 设置自定义日志上报的级别，默认不上报自定义日志
#endif
```



## 关于 SDK 回调问题

`BuglyConfig.h`文件中还有一个可以遵守的协议 `BuglyDelegate` 用于在系统崩溃时可以同时上传自定义数据。

```objective-c
@protocol BuglyDelegate <NSObject>

@optional
/**
 *  发生异常时回调
 *
 *  @param exception 异常信息
 *
 *  @return 返回需上报记录，随异常上报一起上报
 */
- (NSString * BLY_NULLABLE)attachmentForException:(NSException * BLY_NULLABLE)exception;

@end
```

我遇到的问题是，出于代码清晰的目的，将应用初始化配置、第三方框架初始化配置、推送、分享相关的代码都放在分类中了：

![image](http://upload-images.jianshu.io/upload_images/2648731-4453a2d2cb9ad751.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

并且在分类中遵守了 `<BuglyDelegate>` 协议，也实现了简单的回调方法：

```objective-c
- (NSString * BLY_NULLABLE)attachmentForException:(NSException * BLY_NULLABLE)exception {
    return @"需要上传的数据...";
}
```

正常情况下，应用发生崩溃后，Bugly 会立即上报异常，同时在「崩溃分析」-「跟踪数据」-「附件信息」中显示回调方法中上传的数据文件 **crash_attach.log**：

![image](http://upload-images.jianshu.io/upload_images/2648731-a0e1ac5bf7d96730.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

实际上把回调代码写在分类中时，应用发生崩溃时，并不会触发协议方法。所以**务必要把所有与 Bugly 初始化及回调代码写在 `AppDelegate.m	` 文件中**。

最后，附上 Bugly 集成的完整正确代码示例：

```objective-c
#import "AppDelegate.h"
#import <Bugly/Bugly.h>

static NSString *const KBuglyAppId = @"**********";

@interface AppDelegate () <BuglyDelegate>

@end

@implementation AppDelegate

#pragma mark - UIApplicationDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    
    [self p_configureForBugly];
  
    return YES;
}

#pragma mark - Private

- (void)p_configureForBugly {
    BuglyConfig *config = [[BuglyConfig alloc] init];
    config.channel = @"App Store";
    config.blockMonitorEnable = YES; // 卡顿监控开关，默认关闭
    config.blockMonitorTimeout = 5;
    config.unexpectedTerminatingDetectionEnable = YES; // 非正常退出事件记录开关，默认关闭
    config.delegate = self;

#ifdef DEBUG
    config.debugMode = YES; // debug 模式下，开启调试模式
    config.reportLogLevel = BuglyLogLevelVerbose; // 设置自定义日志上报的级别，默认不上报自定义日志
#else
    config.debugMode = NO; // release 模式下，关闭调试模式
    config.reportLogLevel = BuglyLogLevelWarn;
#endif
    
    [Bugly startWithAppId:KBuglyAppId config:config];
}

#pragma mark - BuglyDelegate

- (NSString * BLY_NULLABLE)attachmentForException:(NSException * BLY_NULLABLE)exception {
    NSDictionary *dictionary = @{@"Name":exception.name,
                                 @"Reason":exception.reason};
    return [NSString stringWithFormat:@"Exception:%@",dictionary];
}

@end
```

> 过早的优化是万恶之源。——[高德纳](https://en.wikipedia.org/wiki/Donald_Knuth#Computer_Programming_as_art_.281974.29)


## 对依赖进行抽象化和封装
参考对 Flurry 的封装方法
```
#import <Foundation/Foundation.h>

/**
 封装 Flurry 埋点
 */
@interface HPInstrumentation : NSObject

+(void)startWithAPIKey:(NSString *)apiKey;
+(void)logEvent:(NSString *)name;
+(void)logEvent:(NSString *)name withParams:(NSDictionary *)params;

+(void)logPageViewForTabBarController:(UITabBarController *)vc;

@end

#import "HPInstrumentation.h"
#import "Flurry.h"

@implementation HPInstrumentation

+(void)startWithAPIKey:(NSString *)apiKey
{
	[Flurry startSession:apiKey];
}

+(void)logEvent:(NSString *)name
{
	NSLog(@"<HPInst> %@", name);
	[Flurry logEvent:name];
}

+(void)logEvent:(NSString *)name withParams:(NSDictionary *)params
{
	NSLog(@"<HPInst> %@ -> %@", name, params);
	[Flurry logEvent:name withParameters:params];
}

+(void)logPageViewForTabBarController:(UITabBarController *)vc {
	NSLog(@"<HPInst> PV: %@", [vc class]);
	[Flurry logAllPageViewsForTarget:vc];
}

@end
```

尝试对 Bugly 进行封装
```
#import <Foundation/Foundation.h>

/**
 对依赖进行抽象化和封装
 如果直接在项目中使用崩溃报告系统，那么埋点会分散在项目中的各个角落，不利于后期替换或更改。
 低耦合：增加一个中间层之后，可以随时对依赖的第三方框架进行切换，无须再去修改分散在各个类中的代码。
 */
@interface HQLInstrumentation : NSObject

+ (void)logEvent:(NSString *)name;
+ (void)logEvent:(NSString *)name withParamenters:(NSString *)parameters;

@end

#import "HQLInstrumentation.h"
#import <Bugly/Bugly.h>

@implementation HQLInstrumentation

+ (void)logEvent:(NSString *)name {
    [BuglyLog log:@"%@", name];
}

+ (void)logEvent:(NSString *)name withParamenters:(NSString *)parameters {
    [BuglyLog log:@"%@:%@",name,parameters];
}

@end
```

### 相关框架
- [JJException](https://github.com/jezzmemo/JJException) - 保护 Objective-C 应用不闪退的框架。
- [KSCrash](https://github.com/kstenerud/KSCrash) - iOS崩溃报告框架
- [PLCrashReporter](https://www.plcrashreporter.org/) - Reliable, open-source crash reporting for iOS and Mac OS X.

### 参考
- [Bugly 官方文档](https://bugly.qq.com/docs/release-notes/release-ios-bugly/?v=20171109131920)
- [简书：iOS崩溃异常处理(使用篇) @IUVO](http://www.jianshu.com/p/4d32664dcfdb)
- [简书：iOS集成Bugly异常上报 @逝水流无痕](http://www.jianshu.com/p/846412365179)
- [简书：iOS-Bugly使用 @Caesar大帝归来](http://www.jianshu.com/p/7eecc1a4d2f9)
