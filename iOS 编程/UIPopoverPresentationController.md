# UIPopoverPresentationController API

**UIPopoverPresentationController** 对象用于管理弹窗（popover）内容的显示。 从一个弹窗被呈现到被dismiss过程中，UIKit 使用这个类的实例来管理 presentation 行为。 您可以使用此类的实例来配置（ presentation 样式设置为 `UIModalPresentationPopover` 的）视图控制器的弹出式外观和行为。

## 预览

几乎所有的情况下，你直接使用这个类，而不是创建它的实例。当你使用 `UIModalPresentationPopover` 样式来呈现视图控制器时，UIKIt 会自动创建该类的实例。你可以从显示的视图控制器的`popoverPresentationController` 属性中检索该实例，并使用它来配置 popover 行为。

如果你不想在呈现视图控制器后立即配置弹出窗口，则可以使用委托对象来配置弹出窗口。 在演示过程中，popover演示控制器调用其委托的各种方法 - 遵守 **UIPopoverPresentationControllerDelegate** 协议的对象 - 请求并通知它获取关于演示的状态信息。 您的委托对象可以使用这些方法配置popover并根据需要调整其行为。 有关如何为Popover演示控制器实施委托的信息，请参阅 **UIPopoverPresentationControllerDelegate**。



### 配置需要显示的弹窗

要显示弹窗，设置你的视图控制器的 presentation 样式设置为 `UIModalPresentationPopover` ，并且调用 `presentViewController:animated:completion:` 方法。以 popover 样式呈现视图控制器需要创建一个 popover presentation controller 来管理呈现进程。你可以从被显示的视图控制器的 `popoverPresentationController` 属性中检索该 presentation controller，并使用该对象来配置 popover 行为。

<u>Listing 1</u> 展示了一个样例告诉你如何以 popover 样式来显示视图控制器。设置样式并且调用 `presentViewController:animated:completion:` 方法之后，你可以提取该演示控制器并修改其属性。配置弹出窗口时，请始终指定条形按钮项或源视图和矩形作为弹出窗口的锚点。 您也可以配置其他属性以适应所呈现的内容。

**Listing 1** Presenting a popover

```objective-c
// 使用 popover 样式显示视图控制器.
myPopoverViewController.modalPresentationStyle = UIModalPresentationPopover;
[self presentViewController:myPopoverViewController animated: YES completion: nil];
 
// 获取 popover presentation controller 并且配置它.
UIPopoverPresentationController *presentationController =
         [myPopoverViewController popoverPresentationController];
presentationController.permittedArrowDirections =
         UIPopoverArrowDirectionLeft | UIPopoverArrowDirectionRight;
presentationController.sourceView = myView;
presentationController.sourceRect = sourceRect;
```

调用 `presentViewController:animated:completion:` 方法之后再去配置 popover presentation controller可能看起来反直观，但是在发起显示后，UIKit并不会创建 presentation controller。 此外，UIKit必须等到下一个更新周期才能在屏幕上显示新内容。 这个延迟给你时间来配置你 popover 的 presentation controller。



## Symbols

### 配置 popover 外观

* `popoverLayoutMargins`

  定义允许显示弹出窗口屏幕部分的边距。

  > 一个edge值,能推测与屏幕在旋转之后的弹出窗口的相对位置.

* `backgroundColor`

  popover的背景视图的颜色。

* `passthroughViews`

  用户可以在弹出窗口可见时与之进行交互的视图数组。

* `popoverBackgroundViewClass`

  用于显示popover背景内容的类。

  > 自定义的的背景类.用于替换弹出视图之后的背景.这个类必须继承`UIPopoverBackgroundView`,且必须实现`UIPopoverBackgroundViewMethods`接口的方法.

---



### 指定Popover的锚点

* `barButtonItem`

  锚定弹出窗口的条形按钮项。

  > 将当前锚点设置为`barButtonItem`所在的位置.在弹出窗口时,系统会自动在`barButtonItem`的位置弹出.如果需要修改弹出视图和位置,可以使用`sourceView`和`sourceRect`来替代这个属性
  > 注: `barButtonItem`在设置之后,`sourceView` 和`sourceRect`将会失效

* `sourceView`

  包含popover锚矩形的视图。

  > 箭头所指的对应的视图,`sourceRect`会以这个视图的左上角为原点.使用这个属性和`sourceRect`计算箭头所处的锚点.如果不设置,`sourceRect`就会不起作用

* `sourceRect`

  要在其中锚定popover的指定视图矩形。

  > 箭头所指对应的区域.锚点的计算是这样的.首先根据`sourceView`. 在`sourceView`描绘出一块区域(`CGRect`),然后箭头指向这块区域的中心点.

---



### 委托协议

* `delegate`

  处理与popover相关的代理。

  > 可以控制窗口弹出和消失的行为
  > 注意: 在默认的情况下,**UIPopoverPresentationController**会根据是否是 iphone 和 ipad 来选择弹出的样式,如果当前的设备是 iphone ,那么系统会选择 modal 样式,并弹出到全屏.如果我们需要改变这个默认的行为,则需要实现代理,在代理`- adaptivePresentationStyleForPresentationController:`这个方法中返回一个 **UIModalPresentationNone** 样式

---



### 配置Popover箭头

* `permittedArrowDirections`

  偏好的popover箭头方向。

  > 允许弹出窗口时的箭头方向,设置`barButtonItem`这个属性无效

* `arrowDirection`

  弹出窗口使用的箭头方向。

  > 当前的箭头方向在弹出之前.消失之后,这个值为 `UIPopoverArrowDirectionUnknown`

---



### 实例属性

* `canOverlapSourceViewRect`

  能否超过源视图边距




# UIPopoverPresentationControllerDelegate API

**UIPopoverPresentationControllerDelegate** 协议方法允许您自定义基于Popover的 presentation 行为。 在演示过程中，popover presentation controller会在适当的位置通知您的代理。 您可以使用委托方法来自定义此过程并动态响应更改。



## 预览

定义了采用此协议的对象后，将该对象分配给 **UIPopoverPresentationController** 对象的  `delegate`  属性。 您必须使用 **UIModalPresentationPopover** 样式呈现视图控制器，然后才能获取此类对象。 有关popover演示控制器的更多信息，请参阅 **UIPopoverPresentationController**。



## Symbols

### 呈现和 dismiss popover

* `-prepareForPopoverPresentation:`

  即将要呈现 popover 时通知代理。

* `-popoverPresentationControllerShouldDismissPopover:`

  询问代理是否可以 dismiss  popover 。

* `-popoverPresentationControllerDidDismissPopover:`

  通知代理 popover 已经 dismiss 了。

---



### 重新定位Popover

* `-popoverPresentationController:willRepositionPopoverToRect:inView:`

  告诉代理 UIKit 需要重新定位 popover 的位置。






# 使用

## 默认的模态视图

```objective-c
- (IBAction)button1_clicked:(id)sender {
    DetailViewController *dvc = [[DetailViewController alloc] init];
    [self presentViewController:dvc animated:YES completion:nil];
}
```

![](http://upload-images.jianshu.io/upload_images/2648731-7c3708c060b99ed0.gif?imageMogr2/auto-orient/strip)



## 使用 UIPopoverPresentationController

```objective-c
- (IBAction)button2_clicked:(id)sender {
    // 初始化需要弹出的视图控制器
    DetailViewController *dvc = [[DetailViewController alloc] init];
    // 设置弹出视图控制器大小
    dvc.preferredContentSize = CGSizeMake(100, 200);
    // 使用 popover 样式显示视图控制器
    dvc.modalPresentationStyle = UIModalPresentationPopover;
    
    // 获取 popover presentation controller 并且配置它
    UIPopoverPresentationController *presentationController =
    [dvc popoverPresentationController];
    // 设置委托协议
    presentationController.delegate = self;
    // 设置背景色
    presentationController.backgroundColor = dvc.view.backgroundColor;
    
    // 箭头所指的对应的视图,sourceRect 会以这个视图的左上角为原点.
    presentationController.sourceView = self.button2;
    // 箭头所指对应的区域.在 sourceView 描绘出一块区域(CGRect),然后箭头指向这块区域的中心点.
    presentationController.sourceRect = self.button2.bounds;
    // 设置箭头方向⬅️
    presentationController.permittedArrowDirections =
    UIPopoverArrowDirectionLeft ;
    
    // 以模态形式呈现视图
    [self presentViewController:dvc animated:YES completion:nil];
}

#pragma mark - UIPopoverPresentationControllerDelegate

// 即将要呈现 popover 时通知代理。
- (void)prepareForPopoverPresentation:(UIPopoverPresentationController *)popoverPresentationController {
    NSLog(@"%@",NSStringFromSelector(_cmd));
}

// Called on the delegate when the popover controller will dismiss the popover. Return NO to prevent the dismissal of the view.
- (BOOL)popoverPresentationControllerShouldDismissPopover:(UIPopoverPresentationController *)popoverPresentationController {
    NSLog(@"%@",NSStringFromSelector(_cmd));
    return YES;
}

// Called on the delegate when the user has taken action to dismiss the popover. This is not called when the popover is dimissed programatically.
- (void)popoverPresentationControllerDidDismissPopover:(UIPopoverPresentationController *)popoverPresentationController {
    NSLog(@"%@",NSStringFromSelector(_cmd));
}

// -popoverPresentationController:willRepositionPopoverToRect:inView: is called on your delegate when the
// popover may require a different view or rectangle.
// 告诉代理 UIKit 需要重新定位 popover 的位置。
- (void)popoverPresentationController:(UIPopoverPresentationController *)popoverPresentationController willRepositionPopoverToRect:(inout CGRect *)rect inView:(inout UIView  * __nonnull * __nonnull)view {
    NSLog(@"%@",NSStringFromSelector(_cmd));
    
}

/**
 *  在默认的情况下,UIPopoverPresentationController 会根据是否是 iphone 和 ipad 来选择弹出的样式,如果当前的设备是 iphone ,那么系统会选择 modal 样式,并弹出到全屏.如果我们需要改变这个默认的行为,则需要实现代理,在代理 - adaptivePresentationStyleForPresentationController: 这个方法中返回一个 UIModalPresentationNone 样式
 */
- (UIModalPresentationStyle)adaptivePresentationStyleForPresentationController:(UIPresentationController *)controller {
    return UIModalPresentationNone;
}
```



![](http://upload-images.jianshu.io/upload_images/2648731-ab9393b4ed64b58a.gif?imageMogr2/auto-orient/strip)



##  导航栏按钮使用 UIPopoverPresentationController

```objective-c
#pragma mark - Lifecycle

- (void)viewDidLoad {
    [super viewDidLoad];
    // 设置右侧导航栏按钮
    UIBarButtonItem *rightBarButton = [[UIBarButtonItem alloc] initWithBarButtonSystemItem:UIBarButtonSystemItemAdd
                             target:self
                             action:@selector(barButtonAction:)];
    self.navigationItem.rightBarButtonItem = rightBarButton;
}

#pragma mark - IBActions

- (void)barButtonAction:(id)sender {
    // 初始化需要弹出的视图控制器。
    ListTableViewController *ltvc = [[ListTableViewController alloc] initWithStyle:UITableViewStylePlain];
    // 使用 popover 样式显示视图控制器。
    ltvc.modalPresentationStyle = UIModalPresentationPopover;
    
    // 获取 popover presentation controller 并且配置它。
    UIPopoverPresentationController *presentationController =
    [ltvc popoverPresentationController];
    // 设置遵守委托协议
    presentationController.delegate = self;

    // 将当前锚点设置为 barButtonItem 所在的位置
    presentationController.barButtonItem = self.navigationItem.rightBarButtonItem;
    [self presentViewController:ltvc animated:YES completion:nil];
}



#pragma mark - UIPopoverPresentationControllerDelegate

// 即将要呈现 popover 时通知代理。
- (void)prepareForPopoverPresentation:(UIPopoverPresentationController *)popoverPresentationController {
    NSLog(@"%@",NSStringFromSelector(_cmd));
}

// Called on the delegate when the popover controller will dismiss the popover. Return NO to prevent the dismissal of the view.
- (BOOL)popoverPresentationControllerShouldDismissPopover:(UIPopoverPresentationController *)popoverPresentationController {
    NSLog(@"%@",NSStringFromSelector(_cmd));
    return YES;
}

// Called on the delegate when the user has taken action to dismiss the popover. This is not called when the popover is dimissed programatically.
- (void)popoverPresentationControllerDidDismissPopover:(UIPopoverPresentationController *)popoverPresentationController {
    NSLog(@"%@",NSStringFromSelector(_cmd));
}

// -popoverPresentationController:willRepositionPopoverToRect:inView: is called on your delegate when the
// popover may require a different view or rectangle.
// 告诉代理 UIKit 需要重新定位 popover 的位置。
- (void)popoverPresentationController:(UIPopoverPresentationController *)popoverPresentationController willRepositionPopoverToRect:(inout CGRect *)rect inView:(inout UIView  * __nonnull * __nonnull)view {
    NSLog(@"%@",NSStringFromSelector(_cmd));
    
}

/**
 *  在默认的情况下,UIPopoverPresentationController 会根据是否是 iphone 和 ipad 来选择弹出的样式,如果当前的设备是 iphone ,那么系统会选择 modal 样式,并弹出到全屏.如果我们需要改变这个默认的行为,则需要实现代理,在代理 - adaptivePresentationStyleForPresentationController: 这个方法中返回一个 UIModalPresentationNone 样式
 */
- (UIModalPresentationStyle)adaptivePresentationStyleForPresentationController:(UIPresentationController *)controller {
    return UIModalPresentationNone;
}
```

* 被显示的模态视图是一个  TableView  列表

```objective-c
#import "ListTableViewController.h"

static NSString *reusreIdentifier = @"UITableViewCellStyleDefault";

@interface ListTableViewController ()

@property (nonatomic, copy) NSArray *dataSourceArray;

@end

@implementation ListTableViewController

#pragma mark - Lifecycle

- (void)viewDidLoad {
    [super viewDidLoad];
    [self.tableView registerClass:[UITableViewCell class]
           forCellReuseIdentifier:reusreIdentifier];
//    self.tableView.tableFooterView  = [UIView new];
    self.tableView.scrollEnabled = NO;
}

- (void)didReceiveMemoryWarning {
    [super didReceiveMemoryWarning];
    // Dispose of any resources that can be recreated.
}


#pragma mark - Custom Accessors

- (NSArray *)dataSourceArray {
    if (!_dataSourceArray) {
        _dataSourceArray = @[@"发起群聊",@"添加朋友",@"扫一扫",@"收付款"];
    }
    return _dataSourceArray;
}

#pragma mark - Private
#pragma mark 重写 preferredContentSize, 返回 popover 的大小
/**
 *  此方法会返回一个由 UIKit 子类调用后得到的Size ,此size即是完美适应调用此方法的UIKit子类的size
 *  得到此size后, 可以调用 调整弹框大小的方法 **preferredContentSize** 配合使用
 *  重置本控制器的大小
 */
- (CGSize)preferredContentSize {
    if (self.presentingViewController && self.tableView) {
        CGSize tempSize = self.presentingViewController.view.bounds.size;
        tempSize.width = 150;//
        // 返回一个完美适应tableView的大小的 size;
        // sizeThatFits 返回的是最合适的尺寸, 但不会改变控件的大小
        CGSize size = [self.tableView sizeThatFits:tempSize];
        return size;
    }else{
        return [self preferredContentSize];
    }
}


#pragma mark - UITableViewDataSource

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {
    return self.dataSourceArray.count;
}


- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    UITableViewCell *cell =
        [tableView dequeueReusableCellWithIdentifier:reusreIdentifier
                                        forIndexPath:indexPath];
    cell.textLabel.textColor = [UIColor blueColor];
    cell.textLabel.text = self.dataSourceArray[indexPath.row];
    return cell;
}


#pragma mark - UITableViewDelegate

- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath {
    NSLog(@"selected the row of %ld",indexPath.row);
}

@end

```

![](http://upload-images.jianshu.io/upload_images/2648731-cb98f70d364541f6.gif?imageMogr2/auto-orient/strip)



##  warning

> **[Warning] <_UIPopoverBackgroundVisualEffectView 0x7fa10ef20c60> is being asked to animate its opacity. This will cause the effect to appear broken until opacity returns to 1.**

* [Xcode warning: “This will cause the effect to appear broken until opacity returns to 1”](http://stackoverflow.com/questions/26325141/xcode-warning-this-will-cause-the-effect-to-appear-broken-until-opacity-return)


## 模态视图显示图片

之前的实现方式：

* [自制AlertViewController以及实现UIViewController跳转背景透明效果](http://www.jianshu.com/p/6d790e6eb2ba)



### 源文件

#### HQLImageViewController.h

```objective-c
#import <UIKit/UIKit.h>

@interface HQLImageViewController : UIViewController

@property (nonatomic, strong) UIImage *image;

@end
```

#### HQLImageViewController.m

```objective-c
#import "HQLImageViewController.h"

@interface HQLImageViewController ()

@end

@implementation HQLImageViewController


#pragma mark - Lifecycle

- (void)loadView {
    UIImageView *imageView = [[UIImageView alloc] init];
    imageView.contentMode = UIViewContentModeScaleAspectFit;
    self.view = imageView;
}

- (void)viewWillAppear:(BOOL)animated {
    [super viewWillAppear:animated];
    // 显示图片
    UIImageView *imageView = (UIImageView *)self.view;
    float scaleFactor = MIN(self.view.frame.size.width / self.image.size.width,
                            self.view.frame.size.height / self.image.size.height);
    CGRect popoverFrame = CGRectMake(0,
                                     0,
                                     self.image.size.width * scaleFactor,
                                     self.image.size.height * scaleFactor);
    UIGraphicsBeginImageContextWithOptions(popoverFrame.size, NO, 0.0);
    [self.image drawInRect:popoverFrame];
    UIImage *fitImage = UIGraphicsGetImageFromCurrentImageContext();
    UIGraphicsEndImageContext();
    imageView.image = fitImage;
}

- (void)didReceiveMemoryWarning {
    [super didReceiveMemoryWarning];
    // Dispose of any resources that can be recreated.
}


#pragma mark - Private

// 重写 preferredContentSize, 返回 popover 的大小
// tableView 
- (CGSize)preferredContentSize {
    if (self.presentingViewController) {
        float scaleFactor = MIN(self.view.frame.size.width / self.image.size.width,
                                self.view.frame.size.height / self.image.size.height);
        CGSize fitSize = CGSizeMake(self.image.size.width * scaleFactor,
                                    self.image.size.height * scaleFactor);
        CGSize size = [self.view sizeThatFits:fitSize];
        return size;
    }else{
        return [self preferredContentSize];
    }
}
```

声明遵守委托协议：`UIPopoverPresentationControllerDelegate`

调用：

```objective-c
#pragma mark 显示图片
- (void)showHelpImage:(id)sender{
    [self.view endEditing:YES];    
    HQLImageViewController *imageViewController = [[HQLImageViewController alloc] init];
    imageViewController.modalPresentationStyle = UIModalPresentationPopover;
    NSString *path = [[NSBundle mainBundle] pathForResource:@"boat" ofType:@"png"];
    UIImage *fileImage = [UIImage imageWithContentsOfFile:path];
    imageViewController.image = fileImage;
    imageViewController.preferredContentSize = CGSizeMake(320, 204);
    
    UIPopoverPresentationController *presentationController =
        [imageViewController popoverPresentationController];
    presentationController.delegate = self;
    UIButton *thisButton = sender;
    presentationController.sourceView = thisButton;
    presentationController.sourceRect = thisButton.bounds;
    presentationController.permittedArrowDirections = UIPopoverArrowDirectionUp;
    [self presentViewController:imageViewController animated:YES completion:nil];
}

#pragma mark - UIAdaptivePresentationControllerDelegate
- (UIModalPresentationStyle)adaptivePresentationStyleForPresentationController:(UIPresentationController *)controller {
    return UIModalPresentationNone;
}
```

![](http://upload-images.jianshu.io/upload_images/2648731-9e5f44b6725b5b19.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 添加缩放手势

参考：

* [Gold Challenge Without ScrollView](https://forums.bignerdranch.com/t/gold-challenge-without-scrollview/6077)



```objective-c
- (void)loadView
{
    UIImageView *imageView = [[UIImageView alloc] init];
    imageView.contentMode = UIViewContentModeCenter;
    self.view = imageView;
}
- (void)zoom:(UIGestureRecognizer *)gestureRecognizer
{
    // Cast the gesture recognizer into a pinch gesture recognizer
    UIPinchGestureRecognizer *pinch = (UIPinchGestureRecognizer *)gestureRecognizer;
    NSLog(@"Pinch Scale: %f", pinch.scale);
    CGRect imageFrame = self.view.frame;
    CGRect originalFrame = CGRectMake(0, 0, self.image.size.width, self.image.size.height);
    float scaleFactor = MIN(imageFrame.size.width / self.image.size.width,
                            imageFrame.size.height / self.image.size.height);
    NSLog(@"ScaleFactor: %f", scaleFactor);
    float zoomWidth = pinch.scale * originalFrame.size.width * scaleFactor;
    float zoomHeight = pinch.scale * originalFrame.size.height * scaleFactor;
    CGRect zoomedRect = CGRectMake(0, 0, zoomWidth, zoomHeight);
    NSLog(@"zoomedRect: width: %f height: %f", zoomWidth, zoomHeight);
    NSLog(@"zoomedRect: x:%f, y:%f, width:%f, height:%f", zoomedRect.origin.x, zoomedRect.origin.y, zoomedRect.size.width, zoomedRect.size.height);
    UIGraphicsBeginImageContextWithOptions(zoomedRect.size, NO, 0.0);
    [self.image drawInRect:zoomedRect];
    UIImage *zoomedImage = UIGraphicsGetImageFromCurrentImageContext();
    UIImageView *currentView = (UIImageView *)self.view;
    currentView.image = zoomedImage;
    UIGraphicsEndImageContext();
}
- (void)viewWillAppear:(BOOL)animated
{
    [super viewWillAppear];
    // We must cast the view to UIImageView so the compiler knows it
    // is okay to send it setImage:
    UIImageView *imageView = (UIImageView *)self.view;
    float scaleFactor = MIN(self.view.frame.size.width / self.image.size.width, self.view.frame.size.height / self.image.size.height);
    CGRect popoverFrame = CGRectMake(0, 0, self.image.size.width * scaleFactor, self.image.size.height * scaleFactor);
    UIGraphicsBeginImageContextWithOptions(popoverFrame.size, NO, 0.0);
    [self.image drawInRect:popoverFrame];
    UIImage *fitImage = UIGraphicsGetImageFromCurrentImageContext();
    UIGraphicsEndImageContext();
    imageView.image = fitImage;
}
- (void)viewDidLoad
{
    [super viewDidLoad];
    // Do any additional setup after loading the view.
    self.view.userInteractionEnabled = YES;
    UIPinchGestureRecognizer *pinchRecognizer = [[UIPinchGestureRecognizer alloc] initWithTarget:self action:@selector(zoom:)];
    [self.view addGestureRecognizer:pinchRecognizer];
}
```



# 参考

* [iOS8新特性:UIPopoverPresentationController](http://www.jianshu.com/p/52dd6dec3e9b)

* [弹出菜单 — UIPopoverPresentationController](http://www.jianshu.com/p/422dd88b25b1)

* [Customizing UIPopover with UIPopoverBackgroundView](http://www.scianski.com/customizing-uipopover-with-uipopoverbackgroundview/)

  
