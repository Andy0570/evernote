本文简单介绍 iOS 中 `UITabBarController` 标签页视图控制器的使用方式。

`UITabBarController` 对象可以保存一组视图控制器，让用户轻松地在各个视图控制器上自由切换。此外 `UITabBarController` 对象还会在屏幕底部显示一个标签栏（tab bar），标签栏会有多个标签项（tab item），分别对应  `UITabBarController` 对象中所保存的每一个视图控制器。单击某个标签项，`UITabBarController` 对象就会显示该标签项所对应的视图控制器的视图。

下方的标签栏称为 `UITabBar`，如果 `UITabBarController` 有 4 个子视图控制器，那么 `UITabBar` 内部就会有 4 个 `UITabBarButton` 作为子控件与之对应。

![](https://upload-images.jianshu.io/upload_images/2648731-283538cce0920fe1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

子控制器数量最多不得超过 5 个，即 `UITabBarItem` 数量小于等于 5。

# 示例一：通过**代码方式**创建 `UITabBarController`，并在 `UITabBarController` 上添加两个视图控制器。

## Demo 预览

| 第一页 | 第二页 |
| - | - |
| ![](https://upload-images.jianshu.io/upload_images/2648731-95d16843f5556d61.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/620) | ![](https://upload-images.jianshu.io/upload_images/2648731-4314e53bb08433a6.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/620) |

说明：
* 点击 TabBar 底部的 Home 按钮，显示第一个视图控制器；
* 点击 TabBar 底部的 Second 按钮，显示第二个视图控制器。


## 文档组织结构

![](https://upload-images.jianshu.io/upload_images/2648731-f68014f4c02226f9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/300)

## 实现步骤
1. 新建两个父类为 `UIViewController` 的带 xib 的视图控制器子类对象，**FirstViewController** 和 **SecondViewController**，为方便标识，我们在每个视图控制器的 xib 页面上添加了一个 `UILabel` 标签。
2. 修改 `SceneDelegate.m` 文件，创建 `UITabBarController` 对象，将初始化好的两个视图控制器传入 `UITabBarController` 对象的 `viewControllers ` 数组。最后，将 `UITabBarController` 对象设置为应用窗口的根视图控制器。

```objectivec
#import "SceneDelegate.h"

// Controllers
#import "FirstViewController.h"
#import "SecondViewController.h"

@interface SceneDelegate ()

// 添加选项卡控制器属性，作为容器包含两个视图控制器
@property (strong, nonatomic) UITabBarController *tabBarController;

@end

@implementation SceneDelegate

- (void)scene:(UIScene *)scene willConnectToSession:(UISceneSession *)session options:(UISceneConnectionOptions *)connectionOptions {
    
    // 创建 Window 对象
    self.window = [[UIWindow alloc] initWithWindowScene:(UIWindowScene *)scene];
    self.window.backgroundColor = [UIColor purpleColor];
    
    // 实例化视图控制器对象
    FirstViewController *firstViewController = [[FirstViewController alloc] init];
    SecondViewController *secondViewController = [[SecondViewController alloc] init];
    
    // 初始化选项卡视图控制器
    self.tabBarController = [[UITabBarController alloc] init];
    
    // 将两个视图控制器以数组的式指定给选项卡视图控制器
    // 使应用能够在两个视图控制器的对象之间自由的切换
    NSArray *array = [NSArray arrayWithObjects:firstViewController,secondViewController, nil];
    self.tabBarController.viewControllers = array;
    
    // 设置选项卡视图控制器为当前窗口的根视图控制器
    self.window.rootViewController  = self.tabBarController;
    
    // 设置 Window 为主窗口并显示
    [self.window makeKeyAndVisible];
}
```

注：在 Xcode 11 之前，自定义 `UIWindow` 相关的代码是在 `AppDelegate` 中处理的，在 iOS 13 中，所有 UI 生命周期由  `SceneDelegate`  负责处理，详情可见：[WWDC 2019: Optimizing App Launch](https://developer.apple.com/videos/play/wwdc2019/423/)

除了与以前版本一样，要删除 Main storyboard file base name 之外，还要在项目 `Info.plist` 中，删除 SceneDelegate 的 StoryboardName。

3. 设置每个视图控制器标签的标题和图标。

分别在每个视图控制器的实现文件中覆写视图控制器的指定初始化方法，以设置当前视图控制器标签的标题和图标。

`FirstViewController.m` 文件：
  ```objectivec
- (instancetype)initWithNibName:(NSString *)nibNameOrNil bundle:(NSBundle *)nibBundleOrNil {
    self = [super initWithNibName:nibNameOrNil bundle:nibBundleOrNil];
    if (self) {
        // 设置 tabBar 图标和字体
        self.tabBarItem.title = @"Home";
        self.tabBarItem.image = [UIImage imageNamed:@"home_normal"];
        self.tabBarItem.selectedImage = [UIImage imageNamed:@"home_selected"];
    }
    return self;
}
  ```

`SecondViewController.m` 文件：
  ```objectivec
- (instancetype)initWithNibName:(NSString *)nibNameOrNil bundle:(NSBundle *)nibBundleOrNil {
    self = [super initWithNibName:nibNameOrNil bundle:nibBundleOrNil];
    if (self) {
        // 设置 tabBar 图标和字体
        self.tabBarItem.title = @"Second";
        self.tabBarItem.image = [UIImage imageNamed:@"second_normal"];
        self.tabBarItem.selectedImage = [UIImage imageNamed:@"second_selected"];
    }
    return self;
}
  ```



# 示例二：自定义  `UITabBarController` 子类对象

创建一个 `UITabBarController` 子类对象作为基类，优点就是所有子视图控制器的标签项可以集中在该子类文件中设置，不必分散在各个视图控制器实现文件中。

💡TabBarController 和各视图控制器之间可以通过 StoryBoard 的方式连接。

## Demo 预览

![](http://upload-images.jianshu.io/upload_images/2648731-9dc0cebcb2374543.gif?imageMogr2/auto-orient/strip)

## 代码实现

```objectivec
// 注：kScreenWidth、kScreenHeight 两个宏定义来自 YYKit 框架
- (instancetype)init {
    self = [super init];
    if (self) {
        // 设置背景颜色和大小
        self.view.backgroundColor = COLOR_BACKGROUND;
        self.view.frame = CGRectMake(0, 0, kScreenWidth, kScreenHeight);
        
        [self setViewControllers];
    }
    return self;
}

/*
 * 视图堆栈结构：
 * UITabBarController - UINavigationController - UIViewController （首页）
 *                    - UINavigationController - UIViewController （资讯）
 *                    - UINavigationController - UIViewController （我的）
 *
 */
- (void)setViewControllers {
    // 首页 TabBar
    UIViewController *mainViewController = [self renderTabBarItem:[[HQLMainViewController alloc] init] title:@"首页" imageNamed:@"tab_home_normal" selectedImageNamed:@"tab_home_selected"];
    HQLNavigationController *mainNavigationController = [[HQLNavigationController alloc] initWithRootViewController:mainViewController];
    
    // 资讯 TabBar
    UIViewController *secondViewController = [self renderTabBarItem:[[HQLSecondViewController alloc] init] title:@"资讯" imageNamed:@"tab_news_normal" selectedImageNamed:@"tab_news_selected"];
    HQLNavigationController *secondNavigationController = [[HQLNavigationController alloc] initWithRootViewController:secondViewController];
    
    // 我的 Tabbar
    UIViewController *thirdViewController = [self renderTabBarItem:[[HQLThirdViewController alloc] initWithStyle:UITableViewStyleGrouped] title:@"我的" imageNamed:@"tab_mine_normal" selectedImageNamed:@"tab_mine_selected"];
    HQLNavigationController *thirdNavigationController = [[HQLNavigationController alloc] initWithRootViewController:thirdViewController];
    
    self.viewControllers = @[mainNavigationController,secondNavigationController,thirdNavigationController];
}

- (UIViewController *)renderTabBarItem:(UIViewController *)viewController
                                 title:(NSString *)title
                            imageNamed:(NSString *)normalImgName
                    selectedImageNamed:(NSString *)selectedImgName {
    
    // 设置导航栏的标题为 TabBar 标题
    viewController.title = title;
    
    // 设置导航栏 TabBar
    UIImage *normalImage = [[UIImage imageNamed:normalImgName] imageWithRenderingMode:UIImageRenderingModeAlwaysOriginal];
    UIImage *selectedImage = [[UIImage imageNamed:selectedImgName] imageWithRenderingMode:UIImageRenderingModeAlwaysOriginal];
    viewController.tabBarItem = [[UITabBarItem alloc] initWithTitle:title image:normalImage selectedImage:selectedImage];
    // 设置导航栏被选中时的字体
    [viewController.tabBarItem setTitleTextAttributes:@{NSForegroundColorAttributeName: COLOR_THEME}
                                             forState:UIControlStateSelected];
    return viewController;
}
```

# 其他属性设置

## 去掉TabBar上部的黑色线条,设置 TabBar 透明背景
```objectivec
// 注：imageWithColor: 方法来自 YYKit 框架
[[UITabBar appearance] setBackgroundImage:[UIImage imageWithColor:[UIColor clearColor]]];
[[UITabBar appearance] setShadowImage:[UIImage new]];

UIView *topLineView = [[UIView alloc] init];
topLineView.frame = CGRectMake(0, 0, CGRectGetWidth(self.tabBar.bounds), 1);
topLineView.backgroundColor = [UIColor colorWithWhite:0.966 alpha:1.000];
[self.tabBar addSubview:topLineView];
```
