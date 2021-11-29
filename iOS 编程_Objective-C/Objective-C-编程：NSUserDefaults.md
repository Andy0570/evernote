本文内容：通过 `NSUserDefaults` 将应用版本号存储到偏好设置中。

场景描述：判断应用当前版本号是否与偏好设置中存储的版本号相同，相同则指向常规操作，不同则指向额外的操作，并且把应用当前版本号更新到系统的偏好设置中。该需求常见于应用更新后，需要向用户显示引导页面或者新功能特性页面的情况。

1. 首先，通过以下代码可以获取到应用的版本相关信息：
```objectivec
NSDictionary *infoDic = [[NSBundle mainBundle] infoDictionary];

// 获取当前 APP 版本号，本地程序版本号
NSString *appVersion = [infoDic objectForKey:@"CFBundleShortVersionString"];

// 获取 App 的 build 版本号
NSString *appBuildVersion = [infoDic objectForKey:@"CFBundleVersion"];

// 获取 App 的名称
NSString *appName = [infoDic objectForKey:@"CFBundleDisplayName"];


// ******************************
// 以下四个获取方法摘自 YYCategories：
- (NSString *)appBundleName {
    return [[NSBundle mainBundle] objectForInfoDictionaryKey:@"CFBundleName"];
}

- (NSString *)appBundleID {
    return [[NSBundle mainBundle] objectForInfoDictionaryKey:@"CFBundleIdentifier"];
}

- (NSString *)appVersion {
    return [[NSBundle mainBundle] objectForInfoDictionaryKey:@"CFBundleShortVersionString"];
}

- (NSString *)appBuildVersion {
    return [[NSBundle mainBundle] objectForInfoDictionaryKey:@"CFBundleVersion"];
}
```


2. 创建一个工具类，该工具类的作用是通过偏好设置（`NSUserDefaults`）存储和获取应用程序的版本号。

注意到，这个工具类中的两个方法是类方法！

AppVersionTool.h
```objectivec
#import <Foundation/Foundation.h>

@interface AppVersionTool : NSObject

/**
 *  获取之前保存的版本号
 *
 *  @return NSString 类型的 AppVersion
 */
+ (NSString *)dc_GetLastOneAppVersion;

/**
 *  将新版本号保存或者覆盖到偏好设置中
 */
+ (void)dc_SaveNewAppVersion:(NSString *)version;


@end
```

AppVersionTool.m
```objectivec
#import "AppVersionTool.h"

static NSString * const KAppVersionString = @"AppBundleShortVersionString";

@implementation AppVersionTool

// 获取保存的上一个版本信息
+ (NSString *)dc_GetLastOneAppVersion {
    return [[NSUserDefaults standardUserDefaults] stringForKey:KAppVersionString];
}

// 保存新版本信息（偏好设置）
+ (void)dc_SaveNewAppVersion:(NSString *)version {
    [[NSUserDefaults standardUserDefaults] setObject:version forKey:KAppVersionString];
    [[NSUserDefaults standardUserDefaults] synchronize];
}

@end
```

3. 判断版本号是否相同，并执行相关业务操作。

```objectivec
// 首先获取到应用的当前版本号
NSString *appVersion = [[[NSBundle mainBundle] infoDictionary] objectForKey:@"CFBundleShortVersionString"];
// 将应用的当前版本号与之前存储在偏好设置中的版本号进行判断
BOOL isAppVersionEqual = [appVersion isEqualToString:[DCAppVersionTool dc_GetLastOneAppVersion]];
if (isAppVersionEqual) {
    // 版本号相等，执行相关操作
} else {
    // 版本号不等，执行相关操作
    // 首先要将当前应用的版本号覆盖到偏好设置
    [DCAppVersionTool dc_SaveNewAppVersion:appVersion];
    
    // 其次执行相关操作
}
```


优化点：当需要向首次下载应用的用户显示引导页面时，其实根本不需要使用到「系统版本号」并进行是否相等的判断，可以存储一个 `BOOL` 值来表示引导页是否已经显示过即可。示例代码如下：

```objectivec
static NSString * const kUserHasShowIntroViewKey = @"user_has_introView";

// 保存
NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
[defaults setBool:YES forKey:kUserHasShowIntroViewKey];
[defaults synchronize];

// 读取
BOOL kUserHasShowIntroView = [[NSUserDefaults standardUserDefaults] boolForKey:kUserHasShowIntroViewKey];
```


### 参考

* [(iOS) 新特性页面实现思路](https://www.jianshu.com/p/b5b7904ca530)
* [(iOS) 新特性 - CDDNewFeatures](https://www.jianshu.com/p/856cba4f58c5)
