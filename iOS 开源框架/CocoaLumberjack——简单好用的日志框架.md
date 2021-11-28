* GitHub: [CocoaLumberjack](https://github.com/CocoaLumberjack/CocoaLumberjack)
* star: 11.9k

![](http://upload-images.jianshu.io/upload_images/2648731-91234ade511187ff.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**CocoaLumberjack** 是一个适用于 Mac 和 iOS 的快速、简单、功能强大且灵活的日志框架。

## 如何开始

通过 [CocoaPods](http://cocoapods.org) 安装

###  通过 CocoaPods 安装 Swift 版本
```ruby
platform :ios, '8.0'

# You need to set target when you use CocoaPods 1.0.0 or later.
target 'SampleTarget' do 
  use_frameworks!
  pod 'CocoaLumberjack/Swift'
end
```
注意: `Swift` 子空间中包含了所有的 Objectice-C 代码和 Swift 代码，所以这是足够的。
For more details about how to use Swift with Lumberjack, see [this conversation](https://github.com/CocoaLumberjack/CocoaLumberjack/issues/405).


#### Swift 用法

如果你通过 CocoaPods 或者手动方式导入框架：

```Swift
import CocoaLumberjack
```

```Swift
DDLog.add(DDTTYLogger.sharedInstance) // TTY = Xcode console
DDLog.add(DDASLLogger.sharedInstance) // ASL = Apple System Logs

let fileLogger: DDFileLogger = DDFileLogger() // File Logger
fileLogger.rollingFrequency = TimeInterval(60*60*24)  // 24 hours
fileLogger.logFileManager.maximumNumberOfLogFiles = 7
DDLog.add(fileLogger)

...

DDLogVerbose("Verbose");
DDLogDebug("Debug");
DDLogInfo("Info");
DDLogWarn("⚠️");
DDLogError("Error");
```



### 通过 CocoaPods 安装 Objectice-C 版本

对于 Objective-C，请使用以下命令：

```ruby
platform :ios, '8.0'

target 'SampleTarget' do
    pod 'CocoaLumberjack'
end
```


#### Objectice-C 用法

如果你把 **Lumberjack** 作为框架导入，你可以使用 `@import CocoaLumberjack` 或者 `#import <CocoaLumberjack/CocoaLumberjack.h>` 。

```objectivec
[DDLog addLogger:[DDTTYLogger sharedInstance]]; // TTY = Xcode 控制台
[DDLog addLogger:[DDASLLogger sharedInstance]]; // ASL = Apple System Logs 苹果系统日志

// 创建本地日志文件
DDFileLogger *fileLogger = [[DDFileLogger alloc] init];
fileLogger.rollingFrequency = 60 * 60 * 24; // 每24小时创建一个新文件
fileLogger.logFileManager.maximumNumberOfLogFiles = 7; // 最多允许创建7个文件
[DDLog addLogger:fileLogger];

...

DDLogVerbose(@"Verbose");
DDLogDebug(@"Debug");
DDLogInfo(@"Info");
DDLogWarn(@"Warn");
DDLogError(@"Error");
```



##### 使用 Carthage (iOS 8+) 安装

[Carthage](https://github.com/Carthage/Carthage) is a lightweight dependency manager for Swift and Objective-C. It leverages CocoaTouch modules and is less invasive than CocoaPods.

To install with Carthage, follow the instruction on [Carthage](https://github.com/Carthage/Carthage)

Cartfile
```ruby
github "CocoaLumberjack/CocoaLumberjack"
```

- or [install manually](Documentation/GettingStarted.md#manual-installation)
- read the [Getting started](Documentation/GettingStarted.md) guide, check out the [FAQ](Documentation/FAQ.md) section or the other [docs](Documentation/)
- if you find issues or want to suggest improvements, create an issue or a pull request
- for all kinds of questions involving CocoaLumberjack, use the [Google group](http://groups.google.com/group/cocoalumberjack) or StackOverflow (use [#lumberjack](http://stackoverflow.com/questions/tagged/lumberjack)).

## CocoaLumberjack 3

### 迁移到 3.x

* 待定

### 特性

#### Lumberjack 是 快速 & 简单, 而且强大 & 灵活的.

它在概念上与其他流行的日志记录框架类似，如log4j，它是专为 **Objective-C** 设计的，并且利用了诸如多线程，GCD（如果可用），无锁原子操作（lockless atomic operations）和动态性质的 **Objective-C** runtime。

#### Lumberjack 更快速

在大多数情况下，它比 `NSLog` 快一个数量级。

#### Lumberjack 是简单的

当应用程序启动时，它几乎只需要一行代码来配置 **lumberjack**。 然后只需用 `DDLog` 语句替换您的 `NSLog` 语句即可。 （并且 `DDLog` 宏与 `NSLog` 具有完全相同的格式和语法，因此它非常简单。）

#### Lumberjack 是强大的

一条日志语句可以被发送到多个记录器上，这意味着你可以同时登录到文件和控制台。 想要更多？ 创建自己的记录器（很简单），并通过网络发送您的日志。 或者到数据库或分布式文件系统。 限制它的只有天空(The sky is the limit)。

#### Lumberjack 是灵活的:

配置你需要记录的日志。 更改每个文件的日志级别（便于完美调试）。更改每个记录器的日志级别（详细的控制台，但简明的日志文件）。 更改每个 Xcode 配置的日志级别（详细调试，但简明扼要）。 将日志语句从发行版本中编译出来。 自定义应用程序的日志级别数量。 添加适合你自己的日志。 在运行时动态更改日志级别。 选择如何以及何时希望日志文件滚动。 将日志文件上传到中央服务器。 压缩归档日志文件以节省磁盘空间...

### 这个框架就是为你准备的如果:

-   你正在寻找一种方法来追踪不可再现的错误，这些错误在该领域不断涌现。
-   你对 iPhone 上的超短控制台日志感到沮丧。
-   你正在寻求在支持性和稳定性方面将应用提升到一个新的水平。
-   你正在为你的应用程序（Mac或iPhone）寻找企业级日志记录解决方案。

## 文档

- **[Get started using Lumberjack](Documentation/GettingStarted.md)**
- [Different log levels for Debug and Release builds](Documentation/XcodeTricks.md)
- [Different log levels for each logger](Documentation/PerLoggerLogLevels.md)
- [Use colors in the Xcode debugging console](Documentation/XcodeColors.md)
- [Write your own custom formatters](Documentation/CustomFormatters.md)
- [FAQ](Documentation/FAQ.md)
- [Analysis of performance with benchmarks](Documentation/Performance.md)
- [Common issues you may encounter and their solutions](Documentation/ProblemSolution.md)
- [AppCode support](Documentation/AppCode-support.md)
- **[Full Lumberjack documentation](Documentation/)**

## 配置需求 
当前版本的 **Lumberjack** 需要:
- Xcode 11 or later
- Swift 5.0 or later
- iOS 8 or later
- OS X 10.10 or later
- WatchOS 3 or later
- TVOS 9 or later

## 向后兼容性
- for Xcode 7.3 and Swift 2.3, use the 2.4.0 version
- for Xcode 7.3 and Swift 2.2, use the 2.3.0 version
- for Xcode 7.2 and 7.1, use the 2.2.0 version
- for Xcode 7.0 or earlier, use the 2.1.0 version
- for Xcode 6 or earlier, use the 2.0.x version
- for OS X < 10.7 support, use the 1.6.0 version

## 沟通

- If you **need help**, use [Stack Overflow](http://stackoverflow.com/questions/tagged/lumberjack). (Tag 'lumberjack')
- If you'd like to **ask a general question**, use [Stack Overflow](http://stackoverflow.com/questions/tagged/lumberjack).
- If you **found a bug**, open an issue.
- If you **have a feature request**, open an issue.
- If you **want to contribute**, submit a pull request.

## 作者
- [Robbie Hanson](https://github.com/robbiehanson)
- Love the project? Wanna buy me a coffee? (or a beer :D) [[图片上传失败...(image-d928bf-1510647423281)]](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=UZRA26JPJB3DA)

## Collaborators
- [Ernesto Rivera](https://github.com/rivera-ernesto)
- [Dmitry Vorobyov](https://github.com/dvor)
- [Bogdan Poplauschi](https://github.com/bpoplauschi)
- [C.W. Betts](https://github.com/MaddTheSane)

## License

CocoaLumberjack is available under the BSD license. See the [LICENSE file](https://github.com/CocoaLumberjack/CocoaLumberjack/blob/master/LICENSE.txt).


## 项目结构

![](http://upload-images.jianshu.io/upload_images/2648731-7a30a685c34455a5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



💡 光看以上官方的 **README** 文档其实是完全不够的，我发现实际项目中使用时还是有很多注意点，最好详细看看 **Documentation** 里面的文章。

## 使用此框架前，打印日志是这么做的

记录日志的一般做法是在 Release 模式下禁用 `NSLog`，比如在 `.pch` 文件中，通过对环境的判断，对 `NSLog` 做不同的处理。因此，在不使用 **CocoaLumberjack** 之前，我的 `.pch` 预编译头文件中是这样写的：

```objectivec
/****************** 打印日志 ******************/
#ifdef DEBUG
#define HQString [NSString stringWithFormat:@"%s", __FILE__].lastPathComponent
#define HQLog(...) printf("%s: %s 第%d行:\n %s\n\n", [[NSString hql_stringDate] UTF8String], [HQString UTF8String] , __LINE__, [[NSString stringWithFormat:__VA_ARGS__] UTF8String]);
#else
#define  HQLog(...);
#endif	/* DEBUG */
```

还需要为 `NSString` 类创建一个范畴类的方法来格式化输出时间：

```objectivec
+ (NSString *)hql_stringDate {
    NSDateFormatter *dateFormatter = [[NSDateFormatter alloc] init];
    [dateFormatter setDateFormat:@"YYYY-MM-dd hh:mm:ss"];
    NSString *dateString = [dateFormatter stringFromDate:[NSDate date]];
    return dateString;
}
```


## 项目中使用CocoaLumberjack

[Getting started with the CocoaLumberjack framework.](https://github.com/CocoaLumberjack/CocoaLumberjack/blob/master/Documentation/GettingStarted.md)


项目中集成并使用 **CocoaLumberjack**  分为三步：

1. 添加 **CocoaLumberjack** 文件到项目中；
2. 链接配置 **CocoaLumberjack**；
3. 将你的 `NSLog` 语句转换为使用 **CocoaLumberjack** 宏;



### 添加 **CocoaLumberjack** 框架到项目中

通过 Cocoapods 添加：

```ruby
platform :ios, '8.0'
pod 'CocoaLumberjack'
```



### 链接配置 **CocoaLumberjack**
1. 引入头文件

```objectivec
#define LOG_LEVEL_DEF ddLogLevel
#import <CocoaLumberjack/CocoaLumberjack.h>
```

2. 设置全局状态 Log 级别

```objectivec
static const DDLogLevel ddLogLevel = DDLogLevelDebug;
```

3. 以上两个步骤在 `.pch` 中设置如下：

```objectivec
/** 打印日志 */
#define LOG_LEVEL_DEF ddLogLevel
#import <CocoaLumberjack/CocoaLumberjack.h>

#ifdef DEBUG
static const DDLogLevel ddLogLevel = DDLogLevelVerbose;
#else
static const DDLogLevel ddLogLevel = DDLogLevelWarning;
#endif	/* DEBUG */
```

4. `AppDelegate.m`文件

```objectivec
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

    if (@available(iOS 10.0, *)) {
        // Uses os_log
        [DDLog addLogger:[DDOSLogger sharedInstance]];
    } else {
        // TTY = Xcode 控制台
        // DDTTYLogger，你的日志语句将被发送到 Xcode 控制台
        [DDLog addLogger:[DDTTYLogger sharedInstance]];
    }

    // DDASLLogger，你的日志语句将被发送到苹果文件系统、你的日志状态会被发送到 Console.app
    [DDLog addLogger:[DDASLLogger sharedInstance]];

    // DDFileLogger，你的日志语句将写入到一个文件中，默认路径在沙盒的Library/Caches/Logs/目录下，文件名为bundleid+空格+日期.log。
    DDFileLogger *fileLogger = [[DDFileLogger alloc] init];
    fileLogger.rollingFrequency = 60 * 60 * 24; // 刷新频率为24小时
    fileLogger.logFileManager.maximumNumberOfLogFiles = 7; // 保存一周的日志，即7天
    [DDLog addLogger:fileLogger];

    // 日常使用时，打印日志方法如下
    DDLogVerbose(@"Verbose");	// 详细日志
    DDLogDebug(@"Debug");    	// 调试日志
    DDLogInfo(@"Info");     	// 信息日志
    DDLogWarn(@"Warn");			// 警告日志
    DDLogError(@"Error");		// 错误日志

    return YES;
}
```

运行程序，查看 Xocde 控制台日志：

![](https://upload-images.jianshu.io/upload_images/2648731-b6e8f63915c4961a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

💡 你会发现控制台日志打印了两遍，那是因为在配置中我们同时添加了 Xcode 控制台日志和 DDASLLogger 日志，开发中注释掉配置的 DDASLLogger 日志即可。



在项目中使用 CocoaLumberjack 框架前后的变化：

```objectivec
// 原生的 NSLog 方法：
NSLog(@"Broken sprocket detected!");
NSLog(@"User selected file:%@ withSize:%u", filePath, fileSize);

// 集成了 CocoaLumberjack 框架之后的用法：
DDLogError(@"Broken sprocket detected!");
DDLogVerbose(@"User selected file:%@ withSize:%u", filePath, fileSize);
```

正如你所看到的那样，`DDLog` 宏与 `NSLog` 具有完全相同的语法。




## CocoaLumberjack 的日志类型

1. **DDLog**（整个框架的基础）
2. **DDASLLogger**（发送日志到苹果的日志系统，以便它们显示在 Console.app 上）
3. **DDTTYLoyger**（发送日志到 Xcode 控制台）
4. **DDFIleLoger**（把日志写入本地文件）



### CocoaLumberjack 的日志级别

你需要考虑使用哪种日志级别，**CocoaLumberjack** 一共提供了 5 种 `DDLogFlag`：

```objectivec
typedef NS_OPTIONS(NSUInteger, DDLogFlag){
    DDLogFlagError      = (1 << 0),
    DDLogFlagWarning    = (1 << 1),
    DDLogFlagInfo       = (1 << 2),
    DDLogFlagDebug      = (1 << 3),
    DDLogFlagVerbose    = (1 << 4)
};
```

`DDLogLevel` 用来过滤每条日志

```objectivec
typedef NS_ENUM(NSUInteger, DDLogLevel){
    DDLogLevelOff       = 0,
    DDLogLevelError     = (DDLogFlagError),
    DDLogLevelWarning   = (DDLogLevelError   | DDLogFlagWarning),
    DDLogLevelInfo      = (DDLogLevelWarning | DDLogFlagInfo),
    DDLogLevelDebug     = (DDLogLevelInfo    | DDLogFlagDebug),
    DDLogLevelVerbose   = (DDLogLevelDebug   | DDLogFlagVerbose),
    DDLogLevelAll       = NSUIntegerMax
};
```

例如，如果你将日志级别设置为 `DDLogLevelInfo`，那么你会看到 `DDLogLevelError`、`DDLogLevelWarning` 和 `DDLogLevelInfo` 级别的日志。

你也可以[自定义Log级别或者每个级别的名字](https://github.com/CocoaLumberjack/CocoaLumberjack/blob/master/Documentation/CustomLogLevels.md)或者[在单纯的级别上增加一些高级用法](https://github.com/CocoaLumberjack/CocoaLumberjack/blob/master/Documentation/FineGrainedLogging.md)



---

* [Automatically use different log levels per xcode configuration.](https://github.com/CocoaLumberjack/CocoaLumberjack/blob/master/Documentation/XcodeTricks.md)

我们也可以为Debug和Release模式设置不同的日志级别：

Xcode 4+ :

```objectivec
#ifdef DEBUG 
static const DDLogLevel ddLogLevel = DDLogLevelVerbose;
#else 
static const DDLogLevel ddLogLevel = DDLogLevelWarning;
#endif
```



---

* [Use Log Level per Logger](https://github.com/CocoaLumberjack/CocoaLumberjack/blob/master/Documentation/PerLoggerLogLevels.md)

我们还可以为每种 logger 记录器设置不同的日志级别：

如果你需要为每个 logger 记录器使用不同的日志级别（即，如果你有自定义记录器，如Crashlytics记录器，它不应该记录 Info 或 Debug），你就可以使用 `[DDLog addLogger：withLevel：]`方法轻松实现。

```objective-c
[DDLog addLogger:[DDASLLogger sharedInstance] withLevel:DDLogLevelInfo];
[DDLog addLogger:[DDTTYLogger sharedInstance] withLevel:DDLogLevelDebug];
```

你仍然可以使用旧的类方法`+ addLogger：`，该方法使用 **DDLogLevelVerbose** 作为默认值，它不会排除任何日志。

你可以通过 `[DDLog allLoggersWithLevel]`方法检索与 **DDLog** 关联的每个记录器和级别的列表。


### 自定义日志格式

[官方文档自定义日志的formatter格式](https://github.com/CocoaLumberjack/CocoaLumberjack/blob/master/Documentation/CustomFormatters.md)：

```objectivec
// .h
#import <Foundation/Foundation.h>
#import <DDLog.h>

@interface HQLCustomFormatter : NSObject <DDLogFormatter> {
    int atomicLoggerCount;
    NSDateFormatter *threadUnsafeDateFormatter;
}

@end

// .m
#import "HQLCustomFormatter.h"
#import <libkern/OSAtomic.h>

static NSString *const KdateFormatString = @"yyyy/MM/dd HH:mm:ss";

@implementation HQLCustomFormatter

- (NSString *)stringFromDate:(NSDate *)date {
    int32_t loggerCount = OSAtomicAdd32(0, &atomicLoggerCount);
    
    if (loggerCount <= 1) {
        // Single-threaded mode.
        
        if (threadUnsafeDateFormatter == nil) {
            threadUnsafeDateFormatter = [[NSDateFormatter alloc] init];
            [threadUnsafeDateFormatter setDateFormat:KdateFormatString];
        }
        
        return [threadUnsafeDateFormatter stringFromDate:date];
    } else {
        // Multi-threaded mode.
        // NSDateFormatter is NOT thread-safe.
        
        NSString *key = @"MyCustomFormatter_NSDateFormatter";
        
        NSMutableDictionary *threadDictionary = [[NSThread currentThread] threadDictionary];
        NSDateFormatter *dateFormatter = [threadDictionary objectForKey:key];
        
        if (dateFormatter == nil) {
            dateFormatter = [[NSDateFormatter alloc] init];
            [dateFormatter setDateFormat:KdateFormatString];
            
            [threadDictionary setObject:dateFormatter forKey:key];
        }
        
        return [dateFormatter stringFromDate:date];
    }
}

#pragma mark - DDLogFormatter

- (NSString *)formatLogMessage:(DDLogMessage *)logMessage {
    NSString *logLevel; // 日志等级
    switch (logMessage->_flag) {
        case DDLogFlagError    : logLevel = @"Error";   break;
        case DDLogFlagWarning  : logLevel = @"Warning"; break;
        case DDLogFlagInfo     : logLevel = @"Info";    break;
        case DDLogFlagDebug    : logLevel = @"Debug";   break;
        default                : logLevel = @"Verbose"; break;
    }
    
    NSString *dateAndTime = [self stringFromDate:(logMessage.timestamp)]; // 日期和时间
    NSString *logFileName = logMessage -> _fileName; // 文件名
    NSString *logFunction = logMessage -> _function; // 方法名
    NSUInteger logLine = logMessage -> _line;        // 行号
    NSString *logMsg = logMessage->_message;         // 日志消息
    
    // 日志格式：日期和时间 文件名 方法名 : 行数 <日志等级> 日志消息
    return [NSString stringWithFormat:@"%@ %@ %@ : %lu <%@> %@", dateAndTime, logFileName, logFunction, logLine, logLevel, logMsg];
}

- (void)didAddToLogger:(id <DDLogger>)logger {
    OSAtomicIncrement32(&atomicLoggerCount);
}

- (void)willRemoveFromLogger:(id <DDLogger>)logger {
    OSAtomicDecrement32(&atomicLoggerCount);
}

@end
```

最后，自定义好日志之后记得添加到全局的配置中：

```objectivec
[DDTTYLogger sharedInstance].logFormatter = [[HQLCustomFormatter alloc] init]; // 自定义日志
[DDLog addLogger:[DDTTYLogger sharedInstance]]; // TTY = Xcode console
[DDLog addLogger:[DDASLLogger sharedInstance]]; // ASL = Apple System Logs

DDFileLogger *fileLogger = [[DDFileLogger alloc] init]; // File Logger
fileLogger.rollingFrequency = 60 * 60 * 24; // 24 hour rolling
fileLogger.logFileManager.maximumNumberOfLogFiles = 7;
[DDLog addLogger:fileLogger];
```


更多文档详情请查看 [CocoaLumberjack Documentation](https://github.com/CocoaLumberjack/CocoaLumberjack/tree/master/Documentation)



### 遇到的问题

![](http://upload-images.jianshu.io/upload_images/2648731-7a9ea31f196204cf.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/800)

#### 解决方法

参考 [*Getting started with the CocoaLumberjack framework.*](https://github.com/CocoaLumberjack/CocoaLumberjack/blob/master/Documentation/GettingStarted.md)

```objectivec
#define LOG_LEVEL_DEF ddLogLevel // 要先设置宏定义
#import <CocoaLumberjack/CocoaLumberjack.h>

#ifdef DEBUG
static const DDLogLevel ddLogLevel = DDLogLevelVerbose; // ddLogLevel 注意大小写要与上面宏定义中保持一致
#else
static const DDLogLevel ddLogLevel = DDLogLevelWarning;
#endif	/* DEBUG */
```

## 参考

* [CocoaLumberjack：简单好用的Log库](http://www.jianshu.com/p/7b799bef0107)
* [CocoaLumberjack的集成和学习](https://zllbird.github.io/2016/01/09/cocoalumberjack的集成和学习/)
* [源码浅析 - CocoaLumberjack 3.6 之 DDLog](https://mp.weixin.qq.com/s/zfND2woda5zELvJcT73T6w)
