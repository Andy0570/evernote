* GitHub源码: [IQKeyboardManager](https://github.com/hackiftekhar/IQKeyboardManager)
* star: 14.4k+

> 以下内容来源于官方源码、 README 文档、测试 Demo或个人使用总结 ！

![](http://upload-images.jianshu.io/upload_images/2648731-86a3f713e7c705c2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

通常在开发应用程序时，我们会遇到一个问题，iPhone 键盘向上滑动并覆盖 **UITextField / UITextView**。 **IQKeyboardManager** 允许您防止键盘向上滑动就会覆盖 **UITextField / UITextView** 的问题，而不需要您输入任何代码，并且不需要额外的设置。 要使用**IQKeyboardManager**，您只需要向项目中添加源文件。

### 启用/禁用管理键盘和 textField 之间的距离。
**默认全局开启**，只要把框架放入项目中就能自动开启，甚至不需要 `#import` 导入，厉害了。

```objectivec
/**
 Enable/disable managing distance between keyboard and textField. Default is YES(Enabled when class loads in `+(void)load` method).
 */
@property(nonatomic, assign, getter = isEnabled) BOOL enable;
```
使用：
```objectivec
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // 自动管理键盘和textField之间的距离（默认开启）
    [IQKeyboardManager sharedManager].enable = NO;
}
```

### 添加手势，当键盘被弹出后，点击屏幕背景收起键盘。

**方法一：** 如果不使用该框架实现的方式：
1. 在 `viewDidLoad:` 方法中添加以下代码：
 ```objectivec
// 添加手势，点击屏幕其他区域收起键盘的操作
UITapGestureRecognizer *gesture = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(hidenkeyboard)];
gesture.numberOfTapsRequired = 1;
[self.view addGestureRecognizer:gesture];
```

2. 然后添加需要实现的 `hidenkeyboard` 方法：
```objectivec
//隐藏键盘的方法
- (void)hidenkeyboard {
    // 设置所有需要用到键盘的输入文本框放弃第一响应者状态
    [self.view endEditing:YES];

    [self.account resignFirstResponder]; // 用户名输入框
    [self.password resignFirstResponder]; // 密码输入框
}
```

**方法二：** 使用框架的实现方式，一行代码解决：
```objectivec
[IQKeyboardManager sharedManager].shouldResignOnTouchOutside = YES;
```

### 隐藏键盘上的工具条

* 全局设置

```objectivec
[IQKeyboardManager sharedManager].enableAutoToolbar = NO;
```
* 当某个特定输入框不需要键盘上的工具条时：

```objectivec
textField.inputAccessoryView = [[UIView alloc] init];
```

* 在键盘上方显示工具条，不显示文本输入框占位符文字

```objectivec
[IQKeyboardManager sharedManager].enableAutoToolbar = YES;
[IQKeyboardManager sharedManager].shouldShowTextFieldPlaceholder = NO;
```

* 设置某个视图上所有的输入框不需要工具条

```objectivec
- (void)viewWillAppear:(BOOL)animated {
    [super viewWillAppear:animated];
    
    [IQKeyboardManager sharedManager].enableAutoToolbar = NO;
    [IQKeyboardManager sharedManager].shouldShowToolbarPlaceholder = NO;
}

- (void)viewWillDisappear:(BOOL)animated {
    [super viewWillDisappear:animated];

    [IQKeyboardManager sharedManager].enableAutoToolbar = YES;
    [IQKeyboardManager sharedManager].shouldShowToolbarPlaceholder = YES;
}
```

### 参考
* [iOS开发第三方库一 IQKeyboardManager @豆志昂扬](http://www.jianshu.com/p/01c0682003a9)
* [Properties and functions usage](https://github.com/hackiftekhar/IQKeyboardManager/blob/master/PROPERTIES%20%26%20FUNCTIONS.md)
