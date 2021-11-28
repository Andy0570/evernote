* GitHub: [Reachability](https://github.com/tonymillion/Reachability)
* Star: 6.9k


# **WARNING** there have been reports of apps being rejected when Reachability is used in a framework. The only solution to this so far is to rename the class.

警告：该类库可能使用了与 Apple 源码相同的类名，因此提交应用审核时，可能会被拒绝。目前为止，唯一的解决方法就是更改类名。



# Reachability

Reachability 是 Apple 的 `Reachability` 类的替代品。 它兼容自动引用计数（ARC），使用新的 GCD 方法来通知网络状态的变化。

除了标准的 `NSNotification` 之外，它还支持使用 Blocks 块的方式处理网络是否可访问的情况。

最后，你可以指定是否将 WWAN 连接视为“可达”。

*在真实设备上测试之前，请不要开启 BUGS 模式*

**在你开启一个关于 iOS6 / iOS5 版本的构建错误之前，请使用标签3.2或3.1，因为它们支持分配类型。**


## Requirements

Once you have added the `.h/m` files to your project, simply:

* Go to the `Project->TARGETS->Build Phases->Link Binary With Libraries`.
* Press the plus in the lower left of the list.
* Add `SystemConfiguration.framework`.

Boom, you're done.

## 示例

### 一、Blocks 方法示例

使用 Blocks 块来监听网络状态是否改变。该 Blocks 将会在**后台线程**中被调用，因此你需要将UI 更新切换到主线程中。

#### Objective-C 示例

```objectivec
// 创建 reachability 对象
Reachability *reach = [Reachability reachabilityWithHostname:@"www.google.com"];

// 设置网络可以访问的 blocks 回调
reach.reachableBlock = ^(Reachability*reach)
{
	// 记住这里的代码会在后台线程中被调用，如果你想要更新 UI，一定到切换到主线程：
    dispatch_async(dispatch_get_main_queue(), ^{
        NSLog(@"REACHABLE!");
    });
};

// 设置网络不可访问的 blocks 回调
reach.unreachableBlock = ^(Reachability*reach)
{
    NSLog(@"UNREACHABLE!");
};

// 开启监听
[reach startNotifier];
```

### Swift 3 示例

```Swift
import Reachability

var reach: Reachability?

func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
        // Allocate a reachability object
        self.reach = Reachability.forInternetConnection()
        
        // Set the blocks
        self.reach!.reachableBlock = {
            (reach: Reachability?) -> Void in
            
            // keep in mind this is called on a background thread
            // and if you are updating the UI it needs to happen
            // on the main thread, like this:
            DispatchQueue.main.async {
                print("REACHABLE!")
            }
        }
        
        self.reach!.unreachableBlock = {
            (reach: Reachability?) -> Void in
            print("UNREACHABLE!")
        }
        
        self.reach!.startNotifier()
    
        return true
}
```

### 二、`NSNotification` 通知方法示例

此示例将使用 `NSNotification` 来通知网络连接状态的变化。 它们将在主线程上被调用，因此你可以从该函数内部进行UI更新。

此外，它要求`Reachability`对象默认将 WWAN（3G / EDGE / CDMA）蜂窝网络视为不可达的连接（如果你正在编写视频流应用程序（例如，用于保存用户的数据计划），则可以使用此功能）。

#### Objective-C 示例

```objectivec
// 通过指定域名的方式创建 reachability 对象
Reachability* reach = [Reachability reachabilityWithHostname:@"www.google.com"];

// 不判断蜂窝网络是否可用
reach.reachableOnWWAN = NO;

// Here we set up a NSNotification observer. The Reachability that caused the notification
// is passed in the object parameter
[[NSNotificationCenter defaultCenter] addObserver:self
                                         selector:@selector(reachabilityChanged:)
                                             name:kReachabilityChangedNotification
                                           object:nil];

[reach startNotifier];
```

接收通知的方法：

```objectivec
-(void)reachabilityChanged:(NSNotification*)note
{
    Reachability * reach = [note object];
    if([reach isReachable])
    {
        NSString * temp = [NSString stringWithFormat:@"GOOGLE Notification Says Reachable(%@)", reach.currentReachabilityString];
        NSLog(@"%@", temp);
    }
    else
    {
        NSString * temp = [NSString stringWithFormat:@"GOOGLE Notification Says Unreachable(%@)", reach.currentReachabilityString];
        NSLog(@"%@", temp);
    }
}
```



#### Swift 3 示例

```Swift
import Reachability

var reach: Reachability?

func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
    // Allocate a reachability object
    self.reach = Reachability.forInternetConnection()
    
    // Tell the reachability that we DON'T want to be reachable on 3G/EDGE/CDMA
    self.reach!.reachableOnWWAN = false
    
    // Here we set up a NSNotification observer. The Reachability that caused the notification
    // is passed in the object parameter
    NotificationCenter.default.addObserver(
        self,
        selector: #selector(reachabilityChanged),
        name: NSNotification.Name.reachabilityChanged,
        object: nil
    )
    
    self.reach!.startNotifier()
    
    return true
}
        
func reachabilityChanged(notification: NSNotification) {
    if self.reach!.isReachableViaWiFi() || self.reach!.isReachableViaWWAN() {
        print("Service avalaible!!!")
    } else {
        print("No service avalaible!!!")
    }
}
```

## Tell the world

Head over to [Projects using Reachability](https://github.com/tonymillion/Reachability/wiki/Projects-using-Reachability) and add your project for "Maximum Wins!".



## 总结

1. 创建 `Reachability` 对象的方法有以下几种：

```objectivec
+(instancetype)reachabilityWithHostName:(NSString*)hostname; // 指定域名
+(instancetype)reachabilityForInternetConnection; // 通用网络连接
+(instancetype)reachabilityWithAddress:(void *)hostAddress; // 指定地址
+(instancetype)reachabilityForLocalWiFi; // 指定Wi-Fi网络
```

2. 可选设置，是否忽视蜂窝网络可达性

```objectivec
@property (nonatomic, assign) BOOL reachableOnWWAN;
```

3. 监听网络可达性的方法有两种：

Blocks 方式：

```objectivec
@property (nonatomic, copy) NetworkReachable    reachableBlock;
@property (nonatomic, copy) NetworkUnreachable  unreachableBlock;
```

通知方式：

```objectivec
[[NSNotificationCenter defaultCenter] addObserver:self 
                                        selector:@selector(reachabilityChanged:) 
                                            name:kReachabilityChangedNotification 
                                          object:nil];
```

4. 开始、停止监听

```objectivec
-(BOOL)startNotifier;
-(void)stopNotifier;
```

###   示例代码

```objectivec
// 网络可达性检查
Reachability *reachability = [Reachability reachabilityWithHostName:URL_HOST];
reachability.reachableBlock = ^(Reachability *reachability) {
    // 3.0s 后执行加载任务
    // 默认情况下，切换到主线程更新UI，这里延迟3秒是为了多旋转一会菊花用的。
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(3.0 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        // 连接服务器
        [self connectToServer];
    });
};
reachability.unreachableBlock = ^(Reachability *reachability) {
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(3.0 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        self.emptyDescription = @"无法访问服务器，请稍后再试。";
        self.displayEmptyDataSet = YES;
        self.loading = NO;
    });
};
[reachability startNotifier];
```


### 类似框架

1. Apple 官方示例：[Reachability](https://developer.apple.com/library/content/samplecode/Reachability/Introduction/Intro.html#//apple_ref/doc/uid/DTS40007324-Intro-DontLinkElementID_2)
3. [AFNetworking](https://github.com/AFNetworking/AFNetworking) 的 `AFNetworkReachabilityManager` 类

