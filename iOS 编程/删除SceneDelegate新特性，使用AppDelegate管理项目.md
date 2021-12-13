### 新建 Xcode 项目

在 Xcode 11 环境下新建一个 **Single View App** 项目，打开这个新的项目，可以看到 Xcode 除了会自动生成 `AppDelegate` 文件外，还会自动生成一个名为 `SceneDelegate` 的文件。

![项目目录](https://upload-images.jianshu.io/upload_images/2648731-96d242f1e9c75fb2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/800)

⚠️  `SceneDelegate`  是 iOS 13 下的新特性。这是为了实现 iPadOS 支持多窗口功能的结果，如果你不需要多窗口（multiple windows）功能，或者说很多旧应用或者框架还未适配该特性，如果基于 `SceneDelegate` 并且引入未适配的框架运行应用会导致崩溃！所以，下面首先介绍如何删除 `SceneDelegate` 特性，并继续使用旧的 `AppDelegate` 特性管理应用。


1. 首先打开 `Info.plist` 文件，找到下面这两个属性并删除。 

![](https://upload-images.jianshu.io/upload_images/2648731-c42e739b0f6cca40.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/800)

2. 删除 `SceneDelegate` 类文件，也可以保留不用管它，但既然我们用不到就删了吧。
3. 修改 `AppDelegate.h` 文件，加上 `UIWindow` 属性。


```objectivec
#import <UIKit/UIKit.h>

@interface AppDelegate : UIResponder <UIApplicationDelegate>

@property (strong, nonatomic) UIWindow * window;

@end
```

4. 修改 `AppDelegate.m` 文件，设置 `UIWindow`  设置主窗口，并**删除多余的 `<UISceneSession>` 代理协议**。

```objectivec
#import "AppDelegate.h"
#import "ViewController.h"

@interface AppDelegate ()

@end

@implementation AppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    
    self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];
    // 设置根视图控制器
    ViewController *controller = [[ViewController alloc] init];
    // 窗口根视图控制器
    self.window.rootViewController = controller;
    self.window.backgroundColor = [UIColor whiteColor];
    [self.window makeKeyAndVisible];
    
    return YES;
}

// 下面多余的代码请删除

@end
```

键盘快捷键 `cmd + R` 运行项目，应用应该可以正常运行了。



## 参考
* [WWDC2019: Optimizing App Launch](https://developer.apple.com/videos/play/wwdc2019/423/)
* [掘金 - Xcode11 新建工程中的 SceneDelegate @奇舞团（360 集团大前端团队）](https://juejin.im/post/5db6fb53f265da4d226e25f0)
* [掘金 - Xcode11 新变化：SceneDelegate](https://juejin.im/post/5d8b3ebc518825092202f4ab)
* [掘金 - iOS13 Scene Delegate 详解](https://juejin.im/post/5dcb9cb96fb9a04a5c44eb76)

