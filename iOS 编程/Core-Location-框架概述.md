# Core Location

Core Location 框架提供的位置服务可以确定设备的地理位置，海拔高度和方向，或相对于附近 iBeacon （低功耗蓝牙技术）设备的位置。该框架使用设备上所有可用的组件收集数据，包括 Wi-Fi，GPS，蓝牙，磁力计，气压计和蜂窝网络。

你需要创建 [CLLocationManager](https://developer.apple.com/documentation/corelocation/cllocationmanager) 类的实例来配置、开启和结束 Core Location 服务。一个位置管理器对象支持以下与位置相关的活动：

* 标准和重要位置更新。以可配置的准确度跟踪用户当前位置的变化。
* 区域监控（地理围栏）。监视不同的兴趣区域，并在用户进入或离开这些区域时生成位置事件。
* 信标测距。检测并找到附近的信标。
* 指南针导航。从机载指南针报告航向变化。

要使用位置服务，您的应用会向用户请求授权，然后系统会提示用户是否授予或拒绝该请求。



## 为应用添加定位服务

要向你的应用中添加定位服务，你需要使用 [CLLocationManager](https://developer.apple.com/documentation/corelocation/cllocationmanager) 对象，实现指定的 Delegate 协议，并且设置你的应用所需要获取的定位授权模式。当你的应用运行后，它应检查设备是否支持位置服务，配置并启动所需的位置服务，并请求授权以接收用户的位置。



### 确定用户的设备是否支持位置服务

Core Location 框架所需要的硬件并非在所有设备上都存在。在依赖任何特定的位置服务之前，请通过调用列出的 `CLLocationManager` 对象的方法来检查设备是否支持该服务。如果方法返回 `false`，则设备不支持该特定服务。

| 方法                                           | 描述                                            |
| ---------------------------------------------- | ----------------------------------------------- |
| `significantLocationChangeMonitoringAvailable` | 获取设备是否支持显著位置更改的监听              |
| `isMonitoringAvailableForClass`                | 获取是否支持针对某种位置区域改变的监听          |
| `headingAvailable`                             | 获取方向信息服务是否可用                        |
| `isRangingAvailable`                           | 设备是否支持获取到附近 iBeacon 设备的相对距离。 |



### 创建位置管理器和 Delegate

创建 `CLLocationManager` 类的实例，并将对其的强引用（Strong reference）存储在您的应用程序中的某个位置。在涉及该对象的所有任务完成之前，您必须保持对位置管理器对象的强引用。由于大多数位置管理器任务都是异步运行的，因此如果仅仅将位置管理器存储在本地变量中是不够的。

为 delegate 属性分配一个自定义对象，该自定义对象需要遵守 `<CLLocationManagerDelegate>` 协议。在启动任何位置服务之前分配委托对象。系统会从启动相应位置服务的线程中调用您的委托对象的方法。该线程本身必须有一个活跃的运行循环，就像在您的应用程序的主线程那样。



### 在 Delegate 方法中处理错误

在 Delegate 中实现失败相关的方法，以便在设备上的位置服务不可用时优雅地处理失败。如果您尝试启动不可用的服务，`CLLocationManager` 对象会调用其委托人中的一个失败相关方法。例如，如果区域监控不可用，对象会调用`locationManager:monitoringDidFailForRegion:withError:` 方法。当特定的位置服务不可用时，您可能希望更新应用程序中的UI。



### 请求用户授权并处理位置权限变化

确定你的应用所需要的授权状态。当你的用户访问应用程序中与位置相关的功能时，请请求授权使用位置服务。更多信息请参见[选择要请求的位置服务授权](https://developer.apple.com/documentation/corelocation/choosing_the_location_services_authorization_to_request?language=objc)。

实现委托对象中的 `locationManagerDidChangeAuthorization:` 方法，系统会在你的应用程序创建位置管理器时，以及在你的应用程序的授权状态发生变化时，立即调用该方法。使用此方法来响应您的应用程序的授权状态的变化，并为每个状态执行适当的操作。例如，当授权状态发生变化时，您可能希望根据情况打开或关闭您的应用程序的位置功能。



### 启动定位服务并接收事件

在使用 `CLLocationManager` 实例上的其他方法之前，你必须先设置委托对象。接下来，你必须：

* 调用 `CLLocationManager` 中的相应方法，开始事件的传递。
* 在关联的委托对象中接收位置和标题相关的更新。
* 当您的应用程序不再需要接收位置事件时，调用 `CLLocationManager` 中的适当方法来停止服务。

对于使用的服务，请准确配置与该服务相关的任何属性。Core Location 通过在不需要使用相关硬件时关闭硬件来积极管理电源。例如，将定位事件的期望精度设置为一公里，使位置管理器可以灵活地关闭 GPS 硬件，仅依靠 WiFi 或手机蜂窝网络，这可以大大节省手机电量。

定位精度设置相关：

```objc
// 只有当最新的位置与上一次获取的位置之间的距离, 大于这个值时, 才会通过代理告诉外界.
self.locationM.distanceFilter = 100;
	 
/*
定位精确度
kCLLocationAccuracyBestForNavigation  最适合导航
kCLLocationAccuracyBest    		        精度最好的
kCLLocationAccuracyNearestTenMeters   附近 10 米
kCLLocationAccuracyHundredMeters      附近 100 米
kCLLocationAccuracyKilometer          附近 1000 米
kCLLocationAccuracyThreeKilometers    附近 3000 米
*/
self.locationM.desiredAccuracy = kCLLocationAccuracyBest;
```



## 选择要申请的位置服务授权

应用程序拥有的授权状态决定了它是否以及何时接收位置事件。您的应用程序可以请求两种类型的授权。

* **仅在应用使用期间允许**（When In Use）

  当应用处于使用状态时，可以使用所有位置服务并接收事件。一般来说，当 iOS 应用程序处于前台或在后台运行并启用了后台位置服务时，就会被视为正在使用。

* **始终允许**（always）

  你的应用程序可以使用所有位置服务并接收事件，即使用户不知道你的应用程序正在运行。如果你的应用没有运行，系统就会启动你的应用并传送事件。

与用户授权相关的方法：

```objc
//获取当前用户授权类型
/*
CLAuthorizationStatus枚举如下：
kCLAuthorizationStatusNotDetermined       用户目前还没有选择
kCLAuthorizationStatusRestricted          当前应用尚未授权
kCLAuthorizationStatusDenied              用户拒绝使用位置服务
kCLAuthorizationStatusAuthorizedAlways    用户授权始终可以使用位置服务
kCLAuthorizationStatusAuthorizedWhenInUse 用户授权可以在APP使用时使用位置
kCLAuthorizationStatusAuthorized          用户授权使用位置，iOS8 之前
*/
+ (CLAuthorizationStatus)authorizationStatus;
//请求在APP使用时使用用户的位置信息
- (void)requestWhenInUseAuthorization;
//请求始终使用用户的位置信息
- (void)requestAlwaysAuthorization;
```



### 首选使用 App 时允许的授权

只要有可能，请仅仅申请 "When In Use "授权。该模式具有强大的功能，允许您的应用程序：

* 在用户使用应用程序时访问所有可用的位置服务。如果用户停止使用您的应用程序，任何未完成的请求将暂停，直到用户恢复使用您的应用程序。

* 如果你在 Xcode 项目中启用了后台位置更新，即使在应用程序进入后台后，也能继续获得位置更新。更多信息请参见[在后台处理位置事件](https://developer.apple.com/documentation/corelocation/getting_the_user_s_location/handling_location_events_in_the_background?language=objc)。
* 使用位置通知触发器来启动。如果您的应用程序可以依赖用户的交互，请设置一个 [UNLocationNotificationTrigger](https://developer.apple.com/documentation/usernotifications/unlocationnotificationtrigger?language=objc)，以便在用户进入相关区域时通知他们。当用户点击通过通知时，系统会启动应用程序，使其有资格接收位置事件。这种方法可以让用户在相关时刻决定是否与你的应用分享自己的位置。

只有在应用 "使用中" 状态时，才可向具有 `kCLAuthorizationStatusAuthorizedWhenInUse` 的应用提供位置服务。在所有支持 "使用中 "授权的平台上，应用程序被视为使用中的情况:

* 当应用在前台运行时。
* 在应用程序离开前台后的几秒钟内，您的应用程序拥有完成用户发起的任何当前位置任务的短暂宽限期。
* 当应用显示后台位置使用指示器（`showsBackgroundLocationIndicator`）时。在 iOS上，该指示器是屏幕顶部的蓝色条形或药丸；在 watchOS 上，它是一个小图标。



## Core Location 框架结构图


![Core Location 框架结构图](https://upload-images.jianshu.io/upload_images/2648731-8ef445fd1070ac20.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

摘自：[iOS 开发之 CoreLocation 框架使用](https://my.oschina.net/u/2340880/blog/2993709)


### 参考

* [Apple Doc: Core Location](https://developer.apple.com/documentation/corelocation/)
* 👍 [iOS 开发之 CoreLocation 框架使用](https://my.oschina.net/u/2340880/blog/2993709)
* [CoreLocation 中的 Course 和 Heading 简析](https://juejin.im/post/5e96854551882573a25f2e55)
* [CoreLocation框架详解]([http://jackliu17.github.io/2016/06/10/CoreLocation%E6%A1%86%E6%9E%B6%E8%AF%A6%E8%A7%A3/](http://jackliu17.github.io/2016/06/10/CoreLocation框架详解/))
* [CoreLocation 教程](https://developerdoc.com/essay/CoreLocation/)
* [GitHub 开源框架：intuit/LocationManager](https://github.com/intuit/LocationManager)


