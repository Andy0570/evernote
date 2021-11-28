### 常用方法

```objective-c
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
  
    // 1.创建 UIWindow 实例。
    self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];

    // 2.指定 UIWindow 对象的根视图控制器。
    ViewController *viewController = [[ViewController alloc] init];
    self.window.rootViewController = viewController;

    // 3.设置背景色。
    self.window.backgroundColor = [UIColor whiteColor];

    // 4.设置 UIWindow 实例为主窗口并使其可见。
    [self.window makeKeyAndVisible];
  
    return YES;
}
```

* UIWindow 对象提供了一个方法 `setRootViewController` 将视图控制器的视图层次结构加入应用窗口。当程序将某个视图控制器设置为 UIWindow 对象的 `rootViewControl` 时，UIWindow 对象会将该视图控制器的 view 作为子视图加入窗口，同时负责维护 viewController 和 view 对应的生命周期。此外，还会自动调整view的大小，将其设置为与窗口的大小相同。
* `rootViewControl` 的view需要在应用启动完毕之后就显示，所以UIWindow 对象会在设置完 `rootViewControl` 后立刻加载其view。
 

**UIWindow** 的主要作用：

1. 作为 **UIView** 的最顶层容器；包含应用显示所需要的所有 **UIVIew**；
2. 传递触摸消息和键盘事件给 **UIView**；



### WindowLevel

```objective-c
UIKIT_EXTERN const UIWindowLevel UIWindowLevelNormal;	// 层级：0
UIKIT_EXTERN const UIWindowLevel UIWindowLevelAlert;	// 层级：1000
UIKIT_EXTERN const UIWindowLevel UIWindowLevelStatusBar __TVOS_PROHIBITED;	// 层级：2000
```
