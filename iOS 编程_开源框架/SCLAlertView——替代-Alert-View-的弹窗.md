* [GitHub：**SCLAlertView**](https://github.com/dogo/SCLAlertView)
* star:2800+

> 集成时要注意有两个版本，一个 Swift (**SCLAlertView**) 的，一个 Objective-C (**SCLAlertView-Objective-C**) 的。

> 支付宝也在用的一个框架：[ios-custom-alertview](https://github.com/wimagguc/ios-custom-alertview)

SCLAlertView-Objective-C
============

Animated Alert View written in Swift but ported to Objective-C, which can be used as a `UIAlertView` or `UIAlertController` replacement.

![image](http://upload-images.jianshu.io/upload_images/2648731-a77d9563b5dc05e3.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image](http://upload-images.jianshu.io/upload_images/2648731-28eb48cb8e84cc5d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image](http://upload-images.jianshu.io/upload_images/2648731-2580335bccc40702.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image](http://upload-images.jianshu.io/upload_images/2648731-f966c472595d2faf.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image](http://upload-images.jianshu.io/upload_images/2648731-6d8e4190e71fc79b.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image](http://upload-images.jianshu.io/upload_images/2648731-f4d9919addc7b661.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image](http://upload-images.jianshu.io/upload_images/2648731-87cd6fa7483420b0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image](http://upload-images.jianshu.io/upload_images/2648731-ac97775f8aa96c9f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### 链式语法

```Objective-C

SCLAlertViewBuilder *builder = [SCLAlertViewBuilder new]
.addButtonWithActionBlock(@"Send", ^{ /*work here*/ });
SCLAlertViewShowBuilder *showBuilder = [SCLAlertViewShowBuilder new]
.style(SCLAlertViewStyleWarning)
.title(@"Title")
.subTitle(@"Subtitle")
.duration(0);
[showBuilder showAlertView:builder.alertView onViewController:self.window.rootViewController];
// or even
showBuilder.show(builder.alertView, self.window.rootViewController);
```

#### 复杂样式
```Objective-C
NSString *title = @"Title";
NSString *message = @"Message";
NSString *cancel = @"Cancel";
NSString *done = @"Done";

SCLALertViewTextFieldBuilder *textField = [SCLALertViewTextFieldBuilder new].title(@"Code");
SCLALertViewButtonBuilder *doneButton = [SCLALertViewButtonBuilder new].title(done)
.validationBlock(^BOOL{
    NSString *code = [textField.textField.text copy];
    return [code isVisible];
})
.actionBlock(^{
    NSString *code = [textField.textField.text copy];
    [self confirmPhoneNumberWithCode:code];
});

SCLAlertViewBuilder *builder = [SCLAlertViewBuilder new]
.showAnimationType(SCLAlertViewShowAnimationFadeIn)
.hideAnimationType(SCLAlertViewHideAnimationFadeOut)
.shouldDismissOnTapOutside(NO)
.addTextFieldWithBuilder(textField)
.addButtonWithBuilder(doneButton);

SCLAlertViewShowBuilder *showBuilder = [SCLAlertViewShowBuilder new]
.style(SCLAlertViewStyleCustom)
.image([SCLAlertViewStyleKit imageOfInfo])
.color([UIColor blueColor])
.title(title)
.subTitle(message)
.closeButtonTitle(cancel)
.duration(0.0f);

[showBuilder showAlertView:builder.alertView onViewController:self];
```

### 简单使用
```Objective-C
// 1.创建 SCLAlertView 对象

// 方式一：默认方式（当前 UIViewController 窗口）
SCLAlertView *alert = [[SCLAlertView alloc] init];

// 方式二：new window 上创建
SCLAlertView *alert = [[SCLAlertView alloc] initWithNewWindow];

// 方式三：自定义 Alert 宽度
SCLAlertView *alert = [[SCLAlertView alloc] initWithWindowWidth:300.0f];

// 2.设置警告样式并显示
[alert showSuccess:self title:@"Hello World" subTitle:@"This is a more descriptive text." closeButtonTitle:@"Done" duration:0.0f];
```

以上两步就可以创建并显示一个简单的 Alert View：标题+子标题+默认按钮

#### 可设置的 Alert View 类型

```objective-c
// Success
[alert showSuccess:self title:@"Hello World" subTitle:@"This is a more descriptive text." closeButtonTitle:@"Done" duration:0.0f];

// Error
[alert showError:self title:@"Hello Error" subTitle:@"This is a more descriptive error text." closeButtonTitle:@"OK" duration:0.0f];

// Notice
[alert showNotice:self title:@"Hello Notice" subTitle:@"This is a more descriptive notice text." closeButtonTitle:@"Done" duration:0.0f];

// Warning
[alert showWarning:self title:@"Hello Warning" subTitle:@"This is a more descriptive warning text." closeButtonTitle:@"Done" duration:0.0f]; 

// Info
[alert showInfo:self title:@"Hello Info" subTitle:@"This is a more descriptive info text." closeButtonTitle:@"Done" duration:0.0f]; 

 // Edit
[alert showEdit:self title:@"Hello Edit" subTitle:@"This is a more descriptive info text with a edit textbox" closeButtonTitle:@"Done" duration:0.0f];

// Custom
[alert showCustom:self image:[UIImage imageNamed:@"git"] color:color title:@"Custom" subTitle:@"Add a custom icon and color for your own type of alert!" closeButtonTitle:@"OK" duration:0.0f]; 

// Waiting
[alert showWaiting:self title:@"Waiting..." subTitle:@"Blah de blah de blah, blah. Blah de blah de" closeButtonTitle:nil duration:5.0f];

// Question
[alert showQuestion:self title:@"Question?" subTitle:kSubtitle closeButtonTitle:@"Dismiss" duration:0.0f];
```



### 添加多个按钮
```Objective-C
SCLAlertView *alert = [[SCLAlertView alloc] init];

// 是否以水平分布方式显示按钮，默认垂直排列多个按钮
[alert setHorizontalButtons:YES]; 

// 1. Selector 方式
[alert addButton:@"First Button" target:self selector:@selector(firstButton)];

// 2. Block 方式
[alert addButton:@"Second Button" actionBlock:^(void) {
    NSLog(@"Second button tapped");
}];

// 3. Using Blocks With Validation，设置正则表达式验证
[alert addButton:@"Validate" validationBlock:^BOOL {
    BOOL passedValidation = ....
    return passedValidation;

} actionBlock:^{
    // handle successful validation here
}];

// 4.添加按钮，再自定义按钮样式
// 4.1 添加按钮
SCLButton *button = [alert addButton:@"First Button" target:self selector:@selector(firstButton)];

// 4.2 自定义按钮类型
button.buttonFormatBlock = ^NSDictionary* (void)
{
    NSMutableDictionary *buttonConfig = [[NSMutableDictionary alloc] init];

    buttonConfig[@"backgroundColor"] = [UIColor whiteColor];
    buttonConfig[@"textColor"] = [UIColor blackColor];
    buttonConfig[@"borderWidth"] = @2.0f;
    buttonConfig[@"borderColor"] = [UIColor greenColor];

    return buttonConfig;
};

// 添加弹窗声音
alert.soundURL = [NSURL fileURLWithPath:[NSString stringWithFormat:@"%@/right_answer.mp3", [NSBundle mainBundle].resourcePath]];

// 显示弹窗
[alert showSuccess:self title:@"Button View" subTitle:@"This alert view has buttons" closeButtonTitle:@"Done" duration:0.0f];
```

### 添加按钮计时器
```Objective-C
//The index of the button to add the timer display to.
[alert addTimerToButtonIndex:0 reverse:NO];
```

Example:

```Objective-C
SCLAlertView *alert = [[SCLAlertView alloc] init];
[alert addTimerToButtonIndex:0 reverse:YES];
[alert showInfo:self title:@"Countdown Timer" subTitle:@"This alert has a duration set, and a countdown timer on the Dismiss button to show how long is left." closeButtonTitle:@"Dismiss" duration:10.0f];
```


### 添加文本属性
```Objective-C
SCLAlertView *alert = [[SCLAlertView alloc] init];

alert.attributedFormatBlock = ^NSAttributedString* (NSString *value)
{
    NSMutableAttributedString *subTitle = [[NSMutableAttributedString alloc]initWithString:value];

    NSRange redRange = [value rangeOfString:@"Attributed" options:NSCaseInsensitiveSearch];
    [subTitle addAttribute:NSForegroundColorAttributeName value:[UIColor redColor] range:redRange];

    NSRange greenRange = [value rangeOfString:@"successfully" options:NSCaseInsensitiveSearch];
    [subTitle addAttribute:NSForegroundColorAttributeName value:[UIColor greenColor] range:greenRange];

    NSRange underline = [value rangeOfString:@"completed" options:NSCaseInsensitiveSearch];
    [subTitle addAttributes:@{NSUnderlineStyleAttributeName:@(NSUnderlineStyleSingle)} range:underline];

    return subTitle;
};

[alert showSuccess:self title:@"Button View" subTitle:@"Attributed string operation successfully completed." closeButtonTitle:@"Done" duration:0.0f];
```

### 添加文本输入框
```Objective-C
SCLAlertView *alert = [[SCLAlertView alloc] init];

UITextField *textField = [alert addTextField:@"Enter your name"];

[alert addButton:@"Show Name" actionBlock:^(void) {
    NSLog(@"Text value: %@", textField.text);
}];

[alert showEdit:self title:@"Edit View" subTitle:@"This alert view shows a text box" closeButtonTitle:@"Done" duration:0.0f];
```

### 旋转进度条
```Objective-C
SCLAlertView *alert = [[SCLAlertView alloc] init];
    
[alert showWaiting:self title:@"Waiting..." subTitle:@"Blah de blah de blah, blah. Blah de blah de" closeButtonTitle:nil duration:5.0f];
```

### 添加切换按钮
```Objective-C
SCLAlertView *alert = [[SCLAlertView alloc] init];
    
SCLSwitchView *switchView = [alert addSwitchViewWithLabel:@"Don't show again".uppercaseString];
switchView.tintColor = [UIColor brownColor];
    
[alert addButton:@"Done" actionBlock:^(void) {
    NSLog(@"Show again? %@", switchView.isSelected ? @"-No": @"-Yes");
}];
    
[alert showCustom:self image:[UIImage imageNamed:@"switch"] color:[UIColor brownColor] title:kInfoTitle subTitle:kSubtitle closeButtonTitle:nil duration:0.0f];
```

### 添加自定义视图
```Objective-C
SCLAlertView *alert = [[SCLAlertView alloc] init];

UIView *customView = [[UIView alloc] initWithFrame:CGRectMake(0.0f, 0.0f, 215.0f, 80.0f)];
customView.backgroundColor = [UIColor redColor];

[alert addCustomView:customView];

[alert showNotice:self title:@"Title" subTitle:@"This alert view shows a custom view" closeButtonTitle:@"Done" duration:0.0f];
```

### SCLAlertView属性
```Objective-C
//Dismiss on tap outside (Default is NO)，点击灰色区域是否也可以关闭弹窗
alert.shouldDismissOnTapOutside = YES;

//Hide animation type (Default is SCLAlertViewHideAnimationFadeOut)
alert.hideAnimationType = SCLAlertViewHideAnimationSlideOutToBottom;

//Show animation type (Default is SCLAlertViewShowAnimationSlideInFromTop)
alert.showAnimationType =  SCLAlertViewShowAnimationSlideInFromLeft;

//Set background type (Default is SCLAlertViewBackgroundShadow)
alert.backgroundType = SCLAlertViewBackgroundBlur;

//Overwrite SCLAlertView (Buttons, top circle and borders) colors
alert.customViewColor = [UIColor purpleColor];

//Set custom tint color for icon image.
alert.iconTintColor = [UIColor purpleColor];

//Override top circle tint color with background color
alert.tintTopCircle = NO;

//Set custom corner radius for SCLAlertView
alert.cornerRadius = 13.0f;

//Overwrite SCLAlertView background color
alert.backgroundViewColor = [UIColor cyanColor];

//Returns if the alert is visible or not.
alert.isVisible;

//Make the top circle icon larger
alert.useLargerIcon = YES;

//Using sound
alert.soundURL = [NSURL fileURLWithPath:[NSString stringWithFormat:@"%@/right_answer.mp3", [[NSBundle mainBundle] resourcePath]]];


```

### Helpers
```Objective-C
//Receiving information that SCLAlertView is dismissed
[alert alertIsDismissed:^{
    NSLog(@"SCLAlertView dismissed!");
}];
```

#### Alert View Styles，弹窗样式
```Objective-C
typedef NS_ENUM(NSInteger, SCLAlertViewStyle)
{
    SCLAlertViewStyleSuccess,
    SCLAlertViewStyleError,
    SCLAlertViewStyleNotice,
    SCLAlertViewStyleWarning,
    SCLAlertViewStyleInfo,
    SCLAlertViewStyleEdit,
    SCLAlertViewStyleWaiting,
    SCLAlertViewStyleQuestion,
    SCLAlertViewStyleCustom
};
```
#### Alert View hide animation styles，隐藏动画样式
```Objective-C
typedef NS_ENUM(NSInteger, SCLAlertViewHideAnimation)
{
    SCLAlertViewHideAnimationFadeOut,
    SCLAlertViewHideAnimationSlideOutToBottom,
    SCLAlertViewHideAnimationSlideOutToTop,
    SCLAlertViewHideAnimationSlideOutToLeft,
    SCLAlertViewHideAnimationSlideOutToRight,
    SCLAlertViewHideAnimationSlideOutToCenter,
    SCLAlertViewHideAnimationSlideOutFromCenter,
    SCLAlertViewHideAnimationSimplyDisappear
};
```
#### Alert View show animation styles，显示动画样式
```Objective-C
typedef NS_ENUM(NSInteger, SCLAlertViewShowAnimation)
{
    SCLAlertViewShowAnimationFadeIn,
    SCLAlertViewShowAnimationSlideInFromBottom,
    SCLAlertViewShowAnimationSlideInFromTop,
    SCLAlertViewShowAnimationSlideInFromLeft,
    SCLAlertViewShowAnimationSlideInFromRight,
    SCLAlertViewShowAnimationSlideInFromCenter,
    SCLAlertViewShowAnimationSlideInToCenter,
    SCLAlertViewShowAnimationSimplyAppear
};
```

#### Alert View background styles，背景样式
```Objective-C
typedef NS_ENUM(NSInteger, SCLAlertViewBackground)
{
    SCLAlertViewBackgroundShadow,     // 灰色背景
    SCLAlertViewBackgroundBlur,       // 模糊背景
    SCLAlertViewBackgroundTransparent // 透明背景
};
```



### 我的使用示例

版本更新提示

```objective-c
// 弹窗提示
SCLAlertView *alert = [[SCLAlertView alloc] initWithNewWindow];
alert.horizontalButtons = YES; // 水平显示按钮
alert.shouldDismissOnTapOutside = YES; // 点击其他区域隐藏弹窗

// 拒绝按钮
SCLButton *button = [alert addButton:@"残忍拒绝" actionBlock:nil];
button.buttonFormatBlock = ^NSDictionary *{
    NSMutableDictionary *buttonConfig = [[NSMutableDictionary alloc] init];
    buttonConfig[@"backgroundColor"] = [UIColor whiteColor];
    buttonConfig[@"textColor"] = HexColor(@"0x2866BF");
    return buttonConfig;
};

// 下载按钮
[alert addButton:@"前往下载" actionBlock:^{
    // 打开 App Store
    NSString *urlStr = [NSString stringWithFormat:@"itms-apps://itunes.apple.com/app/id836500024"];
    [[UIApplication sharedApplication] openURL:[NSURL URLWithString:urlStr]];
}];

[alert showInfo:self title:@"发现新版本" subTitle:@"您有新的版本可供下载" closeButtonTitle:nil duration:0.0f];
```
效果：
![image](http://upload-images.jianshu.io/upload_images/2648731-2cad140bcf661557.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/670)
