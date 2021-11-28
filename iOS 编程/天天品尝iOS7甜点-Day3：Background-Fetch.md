> 后台提取特性：iOS 可以根据 App 的使用情况自动更新内容，不管app是否正在运行。

后台提取功能可以让你的程序用户体验效果更加的好，如果你的应用是基于互联网数据更新，那么这非常简单的方法，让应用程序时刻获得最新的信息。

### 参考：

- [GitHub 源码：shinobicontrols/iOS7-day-by-day](https://github.com/ShinobiControls/iOS7-day-by-day)
- [天天品尝iOS7甜点 :: Day 1 :: NSURLSession](http://blog.kingiol.com/2014/01/06/ios7-day-by-day-day1-nsurlsession/)

### Enabling background fetch - 开启后台提取功能

![](http://upload-images.jianshu.io/upload_images/2648731-a0ae905e1631ed12.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### 指定后台提取功能运行的频率

```objective-c
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // 指定后台提取功能运行的频率
    [[UIApplication sharedApplication] setMinimumBackgroundFetchInterval:UIApplicationBackgroundFetchIntervalMinimum];
    
    return YES;
}
```



### 后台提取功能需要实现的代理协议

```objective-c
// application:performFetchWithCompletionHandler:
- (void)application:(UIApplication *)application performFetchWithCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
{
    // 得到要更新的视图控制器
    SCViewController *vc = (SCViewController *)self.window.rootViewController;
    // Insert status updates and pass in the completion handler block
    // 更新状态，并回传更新完成后的处理程序
    NSUInteger numberInserted = [vc insertStatusObjectsForFetchWithCompletionHandler:completionHandler];
    

    // 分 iOS 版本，设置 applicationIconBadgeNumber
    float version = [[[UIDevice currentDevice] systemVersion] floatValue];
    
    if (version >= 8.0) {
        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeBadge categories:nil];
        // 注册用户通知：
        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        // 注册消息推送：
        [application registerForRemoteNotifications];
    }
    
    // 应用程序右上角数字
    [UIApplication sharedApplication].applicationIconBadgeNumber += numberInserted;
}
```



### Testing - 测试

需要测试两种情况：

1. App currently runing in background - app当前在后台运行
2. App currently in terminated state - app当前已经终止进程
