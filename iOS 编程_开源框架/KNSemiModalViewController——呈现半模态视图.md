* **GitHub 地址：** [**KNSemiModalViewController**](https://github.com/kentnguyen/KNSemiModalViewController)
* **star:**1700+

>                                 ⭐️⭐️⭐️ 
以下内容来源于官方源码、 README 文档、测试 Demo或个人使用总结 ！

## UIViewController+KNSemiModal Category

UIViewController+KNSemiModal is an effort to make a replica of **semi-modal view with pushed-back stacked animation** found in the beautiful [Park Guides by National Geographic](http://itunes.apple.com/us/app/national-parks-by-national/id518426085?mt=8) app. You can see this original semi-modal view below.

This library (ARC) is designed as a Category to UIViewController so you don't have to subclass and you can simply drop in any project and it will just work!

**UIViewController+KNSemiModal** 可以实现半模态视图的堆叠效果，使用范畴（Category）实现，代码侵入性较小。

*Original screenshot*

![](http://upload-images.jianshu.io/upload_images/2648731-6b4606f4fea3c475.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) . ![](http://upload-images.jianshu.io/upload_images/2648731-3363437d3a52e8e3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



Replica (view demo video to see the beautiful animation)*

![](http://upload-images.jianshu.io/upload_images/2648731-5b2f8198409fccfa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) . ![](http://upload-images.jianshu.io/upload_images/2648731-496d38bbce991e8d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

On iPad

![](http://upload-images.jianshu.io/upload_images/2648731-779a80a297c8a038.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 



### Features
* Works with bare UIViewController
* Works with UIViewController contained inside UINavigationController
* Works with UIViewController contained inside UINavigationController, contained inside UITabbarController
* Auto handling of modal frame size
* Auto handling of touch area for dismissal
* Resizable after presenting so that keyboard related interactions are possible
* Easy to understand and very small code base, only 2 files
* Trivial to implement as subclass
* Landscape support (not during presentation)
* Only use basic CAAnimation
* iPad support (experimental)
* Minimum iOS 5.0 (if you need 4.x support, use older commits before Jan 2013)

### Optional parameters

- animation duration
- parent alpha
- optional push-back
- shadow opacity
- disabling the cancel action
- transition style: slide up, fade

Easily extend this to anything you would want to make configurable. Feel free to submit pull requests.

可选参数：

```objective-c
// 结构，相关的配置参数
extern const struct KNSemiModalOptionKeys {
	__unsafe_unretained NSString *traverseParentHierarchy; // 遍历父层次结构，BOOL类型. default is YES.
	__unsafe_unretained NSString *pushParentBack;		   // 推回父视图，BOOL类型. default is YES.
	__unsafe_unretained NSString *animationDuration; // 动画延时，double 类型, in seconds. default is 0.5.
	__unsafe_unretained NSString *parentAlpha;       // 父视图透明度，float 类型.值越小越暗. default is 0.5.
    __unsafe_unretained NSString *parentScale;       // 父视图缩放比例，double 类型 default is 0.8
	__unsafe_unretained NSString *shadowOpacity;     // 阴影不透明度，default is 0.8
	__unsafe_unretained NSString *transitionStyle;	 // 动画类型，NSNumber 类型
    __unsafe_unretained NSString *disableCancel;     // 是否禁用取消，BOOL 类型，default is NO.
    __unsafe_unretained NSString *backgroundView;    // 自定义背景视图，UIView 类型
} KNSemiModalOptionKeys;

// 动画类型
NS_ENUM(NSUInteger, KNSemiModalTransitionStyle) {
	KNSemiModalTransitionStyleSlideUp,      // 向上滑动
	KNSemiModalTransitionStyleFadeInOut,    // 淡入淡出
	KNSemiModalTransitionStyleFadeIn,       // 淡入
	KNSemiModalTransitionStyleFadeOut,      // 淡出
};

// 使用时要根据指定类型封装
-(void)kn_registerDefaultsAndOptions:(NSDictionary*)options {
	[self ym_registerOptions:options defaults:@{
     KNSemiModalOptionKeys.traverseParentHierarchy : @(YES),
     KNSemiModalOptionKeys.pushParentBack : @(YES),
     KNSemiModalOptionKeys.animationDuration : @(0.5),
     KNSemiModalOptionKeys.parentAlpha : @(0.5),
     KNSemiModalOptionKeys.parentScale : @(0.8),     
     KNSemiModalOptionKeys.shadowOpacity : @(0.8),
     KNSemiModalOptionKeys.transitionStyle : @(KNSemiModalTransitionStyleSlideUp),
     KNSemiModalOptionKeys.disableCancel : @(NO),
	 }];
}

    NSDictionary *options = @{
                  // 遍历父层次结构，BOOL类型. default is YES.
                  KNSemiModalOptionKeys.traverseParentHierarchy:@(YES),
                  // 父视图后移动画，BOOL类型. default is YES.
                  KNSemiModalOptionKeys.pushParentBack:@(NO),
                  // 动画延时，double 类型. in seconds. default is 0.5.
                  KNSemiModalOptionKeys.animationDuration:@(0.25),
                  // 父视图透明度，float 类型.值越小越暗. default is 0.5.
                  KNSemiModalOptionKeys.parentAlpha:@(0.5),
                  // 父视图缩放比例，double 类型 default is 0.8
                  KNSemiModalOptionKeys.parentScale:@(1),
                  // 阴影不透明度，default is 0.8
                  KNSemiModalOptionKeys.shadowOpacity:@(0.6),
                  // 动画类型，NSNumber 类型
                  KNSemiModalOptionKeys.transitionStyle:        @(KNSemiModalTransitionStyleFadeInOut),
                  // 是否禁用取消，BOOL 类型，default is NO.
                  KNSemiModalOptionKeys.disableCancel:@(NO),
                  // 自定义背景视图，UIView 类型
                  KNSemiModalOptionKeys.backgroundView:[UIView new]
                              };
```





### Installation / How to use

* Copy 4 files in `Source` folder to your project
* Add `QuartzCore.framework` to your project
* `#import "UIViewController+KNSemiModal.h"` in your ViewController
* Call `[self presentSemiModalView:myView]`
* Call `[self dismissSemiModalView]` either from parent/presenting or child/presented controller




# 使用

## 官方 Demo1

显示视图

![](http://upload-images.jianshu.io/upload_images/2648731-b19c42e4e9ceb671.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```objective-c
- (IBAction)buttonDidTouch:(id)sender {
  // 你可以呈现一个简单的 UIImageView 或任何其他的 UIView ，而且不需要关心关闭方法
  UIImageView * imagev = [[UIImageView alloc] initWithImage:[UIImage imageNamed:@"temp.jpg"]];
  UIImageView * bgimgv = [[UIImageView alloc] initWithImage:[UIImage imageNamed:@"background_01"]];
  [self presentSemiView:imagev
            withOptions:@{
                          KNSemiModalOptionKeys.backgroundView:bgimgv
                          }];
}
```



## 官方 Demo2：

显示大小可以改变的视图

![](http://upload-images.jianshu.io/upload_images/2648731-ccf241a7a3175ff4.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

部分代码：

```objective-c
@implementation KNSecondViewController

- (id)initWithNibName:(NSString *)nibNameOrNil bundle:(NSBundle *)nibBundleOrNil {
    self = [super initWithNibName:nibNameOrNil bundle:nibBundleOrNil];
    if (self) {
      self.title = @"Second";
      self.tabBarItem.image = [UIImage imageNamed:@"second"];

      // 请注意，你需要拥有正在呈现的ViewController的所有权
      semiVC = [[KNThirdViewController alloc] initWithNibName:@"KNThirdViewController" bundle:nil];

      // 您可以选择收听通知
      // 视图显示完成后
      [[NSNotificationCenter defaultCenter] addObserver:self
                                               selector:@selector(semiModalPresented:)
                                                   name:kSemiModalDidShowNotification
                                                 object:nil];
      // 视图隐藏后
      [[NSNotificationCenter defaultCenter] addObserver:self
                                               selector:@selector(semiModalDismissed:)
                                                   name:kSemiModalDidHideNotification
                                                 object:nil];
      // 视图被重新调整大小后
      [[NSNotificationCenter defaultCenter] addObserver:self
                                               selector:@selector(semiModalResized:)
                                                   name:kSemiModalWasResizedNotification
                                                 object:nil];
    }
    return self;
}

#pragma mark - Demo

- (IBAction)buttonDidTouch:(id)sender {

  // You can also present a UIViewController with complex views in it
  // and optionally containing an explicit dismiss button for semi modal
  [self presentSemiViewController:semiVC withOptions:@{
		 KNSemiModalOptionKeys.pushParentBack    : @(YES),
		 KNSemiModalOptionKeys.animationDuration : @(2.0),
		 KNSemiModalOptionKeys.shadowOpacity     : @(0.3),
	 }];

}

#pragma mark - Optional notifications

- (void) semiModalResized:(NSNotification *) notification {
  if(notification.object == self){
    NSLog(@"The view controller presented was been resized");
  }
}

- (void)semiModalPresented:(NSNotification *) notification {
  if (notification.object == self) {
    NSLog(@"This view controller just shown a view with semi modal annimation");
  }
}
- (void)semiModalDismissed:(NSNotification *) notification {
  if (notification.object == self) {
    NSLog(@"A view controller was dismissed with semi modal annimation");
  }
}

-(void)dealloc {
  [[NSNotificationCenter defaultCenter] removeObserver:self];
}
```

被呈现视图控制器中的动作方法

```objective-c
// 关闭视图
- (IBAction)dismissButtonDidTouch:(id)sender {

  // 调用父控件ViewController 以关闭视图
  // 视图层次结构要小心
  UIViewController * parent = [self.view containingViewController];
  if ([parent respondsToSelector:@selector(dismissSemiModalView)]) {
    [parent dismissSemiModalView];
  }

}

// 重新设置大小
- (IBAction)resizeSemiModalView:(id)sender {
  UIViewController * parent = [self.view containingViewController];
  if ([parent respondsToSelector:@selector(resizeSemiView:)]) {
    [parent resizeSemiView:CGSizeMake(320, arc4random() % 280 + 180)];
  }
}
```



## 官方 Demo3：

显示 tableView 列表：

![](http://upload-images.jianshu.io/upload_images/2648731-f0f8df6d928b1c27.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```objective-c
- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath {
  [tableView deselectRowAtIndexPath:indexPath animated:NO];

  // You have to retain the ownership of ViewController that you are presenting
  // ⚠️必须要保留被呈现视图控制器的拥有权
  // ⚠️就是说被呈现的视图控制器要设置成它的属性或者实例对象
  [self presentSemiViewController:modalVC
                      withOptions:@{
                     KNSemiModalOptionKeys.pushParentBack : @(NO),
                     KNSemiModalOptionKeys.parentAlpha : @(0.8)
                                    }];
  
  // 以下的代码是无效的
//  KNModalTableViewController * vc = [[KNModalTableViewController alloc] initWithStyle:UITableViewStylePlain];
//  [self presentSemiViewController:vc];
}
```

TableView 的 `frame` 属性在其指定初始化方法中设置：

```objective-c
- (id)initWithStyle:(UITableViewStyle)style {
  self = [super initWithStyle:style];
  if (self) {
    // 设置 frame
    self.view.frame = CGRectMake(0, 0, 320, 200);
  }
  return self;
}
```



## 以模态方式显示一张图片

![](http://upload-images.jianshu.io/upload_images/2648731-095bbe9100785ad7.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

实现代码：

```objective-c
- (IBAction)buttonDidClicked:(id)sender {
    
    // 加载图片方式一，有缓存
    UIImageView *imageView = [[UIImageView alloc] initWithImage:[UIImage imageNamed:@"boat"]];
    // 加载图片方式二，无缓存
    // NSString *path = [[NSBundle mainBundle] pathForResource:@"boat" ofType:@"png"];
    // UIImage *fileImage = [UIImage imageWithContentsOfFile:path];

    NSDictionary *options = @{
                  // 父视图后移动画，BOOL类型. default is YES.
                  KNSemiModalOptionKeys.pushParentBack:         @(NO),
                  // 动画延时，double 类型. in seconds. default is 0.5.
                  KNSemiModalOptionKeys.animationDuration:      @(0.25),
                  // 父视图透明度，float 类型.值越小越暗. default is 0.5.
                  KNSemiModalOptionKeys.parentAlpha:            @(0.5),
                  // 父视图缩放比例，double 类型 default is 0.8
                  KNSemiModalOptionKeys.parentScale:            @(1),
                  // 阴影不透明度，default is 0.8
                  KNSemiModalOptionKeys.shadowOpacity:          @(0.6),
                  // 动画类型，NSNumber 类型
                  KNSemiModalOptionKeys.transitionStyle:        @(KNSemiModalTransitionStyleFadeInOut),
                  // 是否禁用取消，BOOL 类型，default is NO.
                  KNSemiModalOptionKeys.disableCancel:          @(NO),
                              };
    [self presentSemiView:imageView withOptions:options completion:^{
        NSLog(@"completion!");
    }];
}
```
