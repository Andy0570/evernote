### 需求描述

* 在 **UINavigationController** 中的【A页面】 push 到【B 页面】之前，先显示一个【模态页面】显示使用须知、服务条款等内容。
* 在【模态页面】中，如果点击“返回”按钮，则关闭【模态页面】，返回到【A页面】 ；如果点击“同意”按钮，关闭【模态页面】显示【B 页面】



### Demo 

* 【A 页面】→【模态页面】→【A 页面】

![](http://upload-images.jianshu.io/upload_images/2648731-0e8655db6995b037.gif?imageMogr2/auto-orient/strip)

* 【A 页面】→【模态页面】→【B 页面】→【A 页面】

![](http://upload-images.jianshu.io/upload_images/2648731-00b00fa0d07cce09.gif?imageMogr2/auto-orient/strip)



### 源码

##### MainViewController

```objectivec
// 【主页按钮】按下时执行
- (IBAction)buttonDidClicked:(id)sender {
    
    // 模态视图控制器
    ModelViewController *modelViewController = [[ModelViewController alloc] initWithNibName:NSStringFromClass([ModelViewController class]) bundle:nil];
    
    // ‼️ Block回调，push 到第二页
    modelViewController.conformBlock = ^{
        SecondViewController *secondViewController = [[SecondViewController alloc] initWithNibName:NSStringFromClass([SecondViewController class]) bundle:nil];
        [self.navigationController pushViewController:secondViewController animated:NO];
    };
    
    // 你可以直接 present 【模态视图控制器】
    // [self presentViewController:modelViewController animated:YES completion:nil];
    
    // 也可以将【模态视图控制器】封装到【导航视图控制器】中显示。
    UINavigationController *navController = [[UINavigationController alloc] initWithRootViewController:modelViewController];
    [self presentViewController:navController animated:YES completion:nil];
}
```



##### ModelViewController

```objectivec
// .h 文件中设置 Block 属性
@property (nonatomic, copy) void(^conformBlock)(void);

// .m 文件
- (void)setupNavigationBackBarButton {
    self.title = @"模态页面";
    UIBarButtonItem *cancelButtonItem = [[UIBarButtonItem alloc]
        initWithBarButtonSystemItem:UIBarButtonSystemItemCancel
                             target:self
                             action:@selector(cancelButtonDidClicked:)];
    self.navigationItem.leftBarButtonItem = cancelButtonItem;
}

// 取消按钮
- (void)cancelButtonDidClicked:(id)sender {
    [self.navigationController dismissViewControllerAnimated:YES completion:nil];
}

// 关闭模态页面，push 到第二页
- (IBAction)dismissModelViewController:(id)sender {
    // ‼️ 先执行 Block 块 ，第二页视图控制器 push 进导航堆栈后，再关闭模态页面。
    // 如果先关闭模态页面，再 push 第二页视图控制器会影响页面动画过渡上的流畅性。
    self.conformBlock();
    [self.navigationController dismissViewControllerAnimated:YES completion:nil];
}
```



##### SecondViewController

```objectivec
// 返回到根视图控制器
- (IBAction)backToRootViewController:(id)sender {
    [self.navigationController popToRootViewControllerAnimated:YES];
}
```



## 参考

* [IOS 模态视图能实现Push到其他页面的功能吗？](https://segmentfault.com/q/1010000000504594)
* [Pushing another ViewController on a model view](https://stackoverflow.com/questions/8619805/ios-pushing-another-viewcontroller-on-a-model-view)
