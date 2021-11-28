![](https://upload-images.jianshu.io/upload_images/2648731-265d712ca718675d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

INTULocationManager 可以轻松获取 iOS 设备上的位置信息和设备方向信息。它是一个 Objective-C 库，并且也可以在 Swift 中使用。

INTULocationManager 提供基于 Block 块的异步 API，一次性或连续请求获取当前位置信息。它在内部管理多个同时进行的位置和方向请求，每个一次性的位置请求可以指定自己所需的精度等级和超时时间。INTULocationManager 会在第一个请求进来时自动启动位置服务，并在所有请求完成后停止位置服务，同时动态管理位置服务消耗的电量，以减少对电池寿命的影响。



## CLLocationManager 有哪些问题?
CLLocationManager 需要你手动检测和处理诸如权限、停滞/不准确的位置、错误等问题。CLLocationManager 使用了更传统的委托设计模式，而不是更现代的基于 Block 块的回调模式。虽然它可以很好地跟踪用户位置随时间的变化（例如，用于支持转弯的导航系统），但要正确地请求一个一次性的位置信息（例如确定用户当前的城市以获得天气预报，或从当前位置自动填写地址）是非常麻烦的。

INTULocationManager 可以很容易地获取设备的当前位置信息，无论是一次性位置信息还是连续变化的位置信息，以及设备的连续方向。对于一次性的位置请求和重复性的位置更新订阅，API都是非常简单的。对于一次性的位置请求，你可以指定你所需要的位置有多精确，以及你愿意等待多长时间来获得它。还支持重大的位置变化监控。INTULocationManager 通过自动确定和使用最有效的核心位置精度设置，并在不再需要时自动关闭位置服务（如 GPS 或指南针），从而节省设备的电量。



## 安装

*INTULocationManager 需要 iOS 9.0 或更高版本。*

### 使用 [CocoaPods](http://cocoapods.org)

1.	Add the pod `INTULocationManager` to your [Podfile](http://guides.cocoapods.org/using/the-podfile.html).

  ```ruby
pod 'INTULocationManager'
  ```

1.	Run `pod install` from Terminal, then open your app's `.xcworkspace` file to launch Xcode.
1.	Import the `INTULocationManager.h` header.
  * With `use_frameworks!` in your Podfile
    * Swift: `import INTULocationManager`
    * Objective-C: `#import <INTULocationManager/INTULocationManager.h>` (or with Modules enabled: `@import INTULocationManager;`)
  * Without `use_frameworks!` in your Podfile
    * Swift: Add `#import "INTULocationManager.h"` to your bridging header.
    * Objective-C: `#import "INTULocationManager.h"`

### 使用 [Carthage](https://github.com/Carthage/Carthage)

1. Add the `intuit/LocationManager` project to your [Cartfile](https://github.com/Carthage/Carthage/blob/master/Documentation/Artifacts.md#cartfile).

  ```ogdl
  github "intuit/LocationManager"
  ```

1. Run `carthage update`, then follow the [additional steps required](https://github.com/Carthage/Carthage#adding-frameworks-to-an-application) to add the iOS and/or Mac frameworks into your project.
1. Import the INTULocationManager framework/module.
  * Swift: `import INTULocationManager`
  * Objective-C: `#import <INTULocationManager/INTULocationManager.h>` (or with Modules enabled: `@import INTULocationManager;`)

### 手动从 GitHub 导入

1. Download all the files in [INTULocationManager subdirectory](LocationManager/INTULocationManager).
1. Add the source files to your Xcode project (drag and drop is easiest).
1. Import the `INTULocationManager.h` header.
  * Swift: Add `#import "INTULocationManager.h"` to your bridging header.
  * Objective-C: `#import "INTULocationManager.h"`

## 使用

### 申请访问位置服务的权限
当您发出一个位置请求，而用户还没有授予您的应用程序访问该位置服务的权限时，INTULocationManager 会自动处理获取访问位置服务的权限。

#### iOS 9及以上
从 iOS 8.0 开始，你**必须**通过在应用程序的 `Info.plist` 文件中为 `NSLocationWhenInUseUsageDescription` 或 `NSLocationAlwaysUsageDescription` 的键中设置描述字符串，为您的应用程序如何使用位置服务提供描述信息。

INTULocationManager 会根据存在的描述键来决定请求哪个级别的权限。您应该只请求您的应用程序所需的最低权限级别，因此建议您使用 "**仅在应用使用期间允许（When In Use）** "级别的权限，除非您需要更多的访问权限。如果您为这两个描述键都提供了值，则会请求权限更高的 "**始终允许（always）** "级别权限。

#### iOS 11
从 iOS 11 开始，你**必须**通过在应用程序的 `Info.plist` 文件中为 `NSLocationAlwaysAndWhenInUseUsageDescription` 键设置一个字符串，为您的应用程序如何使用位置服务提供描述。

#### iOS 12
从 iOS 12 开始，开发者拥有了将 `desiredActivityType` 设置为 `CLActivityTypeAirborne` 的权限，用于指定定位活动的行为。

```objc
/*
指定定位活动的行为

CLActivityTypeAutomotiveNavigation 汽车导航
CLActivityTypeFitness              步行
CLActivityTypeOtherNavigation      其他导航 如火车 轮船
CLActivityTypeAirborne             飞机导航
CLActivityTypeOther                其他类型
*/
@property(assign, nonatomic) CLActivityType activityType;
```



### 获取当前位置 (一次性)
要获取设备的当前位置，请使用方法 `requestLocationWithDesiredAccuracy:timeout:block:`。

`desiredAccuracy` 参数指定了你所需要的**位置的准确度和最近的位置**。可能的值是：

```objective-c
INTULocationAccuracyCity          // 城市级精度，5000 米或更高，在过去 10 分钟内收到--最低精度。
INTULocationAccuracyNeighborhood  // 邻近地区级精度，1000 米或更远，在过去5分钟内收到。
INTULocationAccuracyBlock         // 街区级精度，100米或更高，在最后1分钟内收到。
INTULocationAccuracyHouse         // 房屋级精度，15米或更高，在过去15秒内收到。
INTULocationAccuracyRoom          // 房间级精度，5米或更高，在过去5秒内收到 -- -- 最高精度。
```

`desiredActivityType` 参数表示被跟踪的**活动类型**。可能的值是：

```objective-c
CLActivityTypeFitness               // 追踪健身活动，如步行、跑步、骑车等。
CLActivityTypeAutomotiveNavigation  // 跟踪汽车的位置更新
CLActivityTypeAirborne              // 追踪空中飞机的活动 - iOS 12及以上版本。
CLActivityTypeOtherNavigation       // 跟踪与汽车无关的车辆导航
CLActivityTypeOther                 // 追踪未知活动。这是默认值
```

`timeout` 参数指定了你愿意等待多长时间，以获得您所请求的精度的位置。超时参数保证您的Block 块将在这段时间内执行，要么是您所请求的至少准确度的位置（INTULocationStatusSuccess），要么是在超时间隔结束前可以确定的任何位置（INTULocationStatusTimedOut）。传入 `0.0` 表示没有超时（不推荐）。

默认情况下，一旦调用 `requestLocationWithDesiredAccuracy:timeout:block:` 方法，超时倒计时就会开始。然而，该方法还有另一个变体，它包含一个`delayUntilAuthorized:` 参数，允许你传递 `YES` 来延迟超时倒计时的开始，直到用户对系统位置服务权限提示做出反应（如果用户还没有允许或拒绝应用访问）。

这是一个实例:
```objectivec
INTULocationManager *locMgr = [INTULocationManager sharedInstance];
[locMgr requestLocationWithDesiredAccuracy:INTULocationAccuracyCity
                                   timeout:10.0
                      delayUntilAuthorized:YES	// 这个参数是可选的，如果省略，默认为NO。
                                     block:^(CLLocation *currentLocation, INTULocationAccuracy achievedAccuracy, INTULocationStatus status) {
                                         if (status == INTULocationStatusSuccess) {
                                           // 请求成功，意味着 achievedAccuracy 至少是请求的精度，
                                           // currentLocation 包含设备的当前位置。
                                         }
                                         else if (status == INTULocationStatusTimedOut) {
                                            // 无法在超时时间内以要求的精度定位用户。
                                            // 然而，currentLocation 包含了目前可用的最佳位置（如果有的话），
                                            // 而academicAccuracy则包含了currentLocation中位置的准确度/近似度信息。
                                         }
                                         else {
                                            // 发生了错误，更多信息可以查看返回的具体状态。
                                         }
                                     }];
```
```swift
let locationManager = INTULocationManager.sharedInstance()
locationManager.requestLocation(withDesiredAccuracy: .city,
                                            timeout: 10.0,
                               delayUntilAuthorized: true) { (currentLocation, achievedAccuracy, status) in
                                   if (status == INTULocationStatus.success) {
                                     // 请求成功，意味着 achievedAccuracy 至少是请求的精度，
                                     // currentLocation 包含设备的当前位置。
                                   }
                                   else if (status == INTULocationStatus.timedOut) {
                                     // 无法在超时时间内以要求的精度定位用户。
                                     // 然而，currentLocation 包含了目前可用的最佳位置（如果有的话），
                                     // 而academicAccuracy则包含了currentLocation中位置的准确度/近似度信息。
                                   }
                                   else {
                                     // 发生了错误，更多信息可以查看返回的具体状态。
                                   }
           }


```

### 订阅以获取持续的位置更新信息

要订阅以获取持续的位置信息更新，请使用 `subscribeToLocationUpdatesWithBlock:` 方法。这个方法指示位置服务使用最高的精度（这也需要最大的功率）。该 Block 块将无限执行（甚至跨越错误，直到取消），每更新一个新的位置就执行一次，无论其准确性如何。

如果你不需要尽可能高的精度级别，你应该使用`subscribeToLocationUpdatesWithDesiredAccuracy:block:` 方法。这个方法采用了期望的准确度级别，并使用它来控制位置服务所使用的功率，较低的准确度级别如 Neighborhood和 City 则需要较少的功率。请注意，INTULocationManager 会自动管理系统的位置服务精度等级，包括当有多个活跃的位置请求/订阅具有不同的期望精度时。

如果发生错误，Block 块将以 `INTULocationStatusSuccess` 以外的状态执行，并且订阅将被保留。

这是一个实例:
```objectivec
INTULocationManager *locMgr = [INTULocationManager sharedInstance];
[locMgr subscribeToLocationUpdatesWithDesiredAccuracy:INTULocationAccuracyHouse
                                                block:^(CLLocation *currentLocation, INTULocationAccuracy achievedAccuracy, INTULocationStatus status) {
                                                    if (status == INTULocationStatusSuccess) {
                                                        // currentLocation 中有一个新的更新位置，
                                                        // 而 achievedAccuracy 表示这个特定位置的准确性。
                                                    } else {
                                                        // 发生了错误，更多信息可查看返回的具体状态。订阅一直保持有效。
                                                    }
                                                }];
```

### 订阅重大位置变更

要订阅重大位置变化，请使用 `subscribeToSignificantLocationChangesWithBlock:` 方法。这将指示位置服务开始监控重大位置变化，这是非常省电的。该 Block 块将无限期执行（直到取消），每更新一个新的位置就执行一次，无论其准确性如何。请注意，如果有其他同时活跃的位置请求或订阅，则该 Block 块将为每次位置更新执行（而不仅仅是重大位置更改）。如果您打算只在位置发生重大变化时才采取行动，您应该根据从最后一个位置接收到的距离和时间实施自定义过滤。

如果发生错误，该 Block 块将以 `INTULocationStatusSuccess` 以外的状态执行，并且该订阅将被保留。

这是一个实例:
```objective-c
INTULocationManager *locMgr = [INTULocationManager sharedInstance];
[locMgr subscribeToSignificantLocationChangesWithBlock:^(CLLocation *currentLocation, INTULocationAccuracy achievedAccuracy, INTULocationStatus status) {
    if (status == INTULocationStatusSuccess) {
    // currentLocation 中有一个新的更新位置，
    // achievedAccuracy 表示这个特定位置的准确性。
    } else {
    // 发生了错误，更多信息可查看返回的具体状态。订阅一直保持有效。
    }
}];
```

如果你的应用程序已获得 "始终允许（always）"位置服务授权，且应用程序终止时至少有一个活跃的重大位置变更订阅，则当系统检测到重大位置变更时，您的应用程序可能会在后台启动。请注意，当应用程序终止时，您在 INTULocationManager 中的所有活动位置请求和订阅将被取消。因此，当应用程序因重大位置变化而启动时，您应立即使用 INTULocationManager 为重大位置变化设置新的订阅，以便接收位置信息。

下面是一个如何处理因位置发生重大变化而在后台启动的实例：
```objective-c
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    // 如果你开始监测重大位置变化，随后你的应用被终止，如果有新的事件到来，系统会自动重新启动应用进入后台。
    // 重新启动后，您仍然必须订阅重要的位置变化以继续接收位置事件。
    if ([launchOptions objectForKey:UIApplicationLaunchOptionsLocationKey]) {
        INTULocationManager *locMgr = [INTULocationManager sharedInstance];
        [locMgr subscribeToSignificantLocationChangesWithBlock:^(CLLocation *currentLocation, INTULocationAccuracy achievedAccuracy, INTULocationStatus status) {
            // 这个区块将被执行，其中包含触发后台应用启动的重大位置变化的详细信息。
            // 并将继续执行未来任何重大的地点变更事件（除非取消）。
        }];
    }
    return YES;
}
```

### 管理活动请求或订阅
在发出位置请求时，可以选择存储请求 ID，允许你在任何时候强制完成或取消请求。
```objective-c
INTULocationManager *locMgr = [INTULocationManager sharedInstance];
INTULocationRequestID requestID = [locMgr requestLocationWithDesiredAccuracy:INTULocationAccuracyHouse
                                                                     timeout:5.0
                                                                       block:locationRequestBlock];

// 强制请求提前完成，类似手动设置超时(将执行该块)
[[INTULocationManager sharedInstance] forceCompleteLocationRequest:requestID];

// 取消请求（不会执行该块）。
[[INTULocationManager sharedInstance] cancelLocationRequest:requestID];
```

请注意，订阅永远不会超时；在订阅上调用 `forceCompleteLocationRequest:` 会简单地取消它。

### 订阅连续的设备方向更新
要订阅连续的标题更新，请使用方法 subscribeToHeadingUpdatesWithBlock:。该方法不设置任何默认的标题过滤值，但您可以使用管理器实例上的 headingFilter 属性来设置。它也不会根据结果的准确性进行过滤，而是让你检查返回的CLHeading对象的 headingAccuracy属性来决定是否可以接受。

该块将无限期执行（直到取消），每更新一个新的标题就执行一次，无论其准确性如何。请注意，如果删除或取消了标题请求，管理器将自动停止更新设备标题，以保护电池寿命。

如果发生错误，该块将以 `INTUHeadingStatusSuccess` 以外的状态执行，并且只有在设备不支持标题时（即状态 `INTUHeadingStatusUnavailable`）才会自动取消订阅。

这是一个实例:
```objective-c
INTULocationManager *locMgr = [INTULocationManager sharedInstance];
[locMgr subscribeToHeadingUpdatesWithBlock:^(CLHeading *heading, INTUHeadingStatus status) {
    if (status == INTUHeadingStatusSuccess) {
        // 已有最新的方向信息
        NSLog(@"'Heading updates' subscription block called with Current Heading:\n%@", heading);
    } else {
        // 发生错误，更多信息可查看返回的具体状态。只有当设备不支持航向时，才会取消订阅。
    }
}];
```

## Example Project
Open the [project](LocationManager) included in the repository (requires Xcode 6 and iOS 8.0 or later). It contains a `LocationManagerExample` scheme that will run a simple demo app. Please note that it can run in the iOS Simulator, but you need to go to the iOS Simulator's **Debug > Location** menu once running the app to simulate a location (the default is **None**).

## Issues & Contributions
Please [open an issue here on GitHub](https://github.com/intuit/LocationManager/issues/new) if you have a problem, suggestion, or other comment.

Pull requests are welcome and encouraged! There are no official guidelines, but please try to be consistent with the existing code style.

## License
INTULocationManager is provided under the MIT license.

# INTU on GitHub
Check out more [iOS and OS X open source projects from Intuit](https://github.com/search?utf8=✓&q=user%3Aintuit+language%3Aobjective-c&type=Repositories&ref=searchresults)!

