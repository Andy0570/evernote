* GitHub源码: [BEMCheckBox](https://github.com/Boris-Em/BEMCheckBox)
* star: 2.6k


![](http://upload-images.jianshu.io/upload_images/2648731-8074a2c5d111ef9d?imageMogr2/auto-orient/strip%7CimageView2/2/w/650)

> [!NOTE]
>
> 以下内容来源于官方源码、 README 文档、测试 Demo 和个人使用总结 ！
>
> **BEMCheckBox** 和普通的 UI 控件用起来相差无几，本文我是对官方 README 的翻译。

**BEMCheckBox** 是一个开源库，可以轻松地为 iOS 创建漂亮、高度可定制的、有动画效果的复选框。

[TOC]



# 项目细节

学习更多关于 **BEMCheckBox** 项目，许可协议，支持等等。

![](http://upload-images.jianshu.io/upload_images/2648731-496e9168a2b369ab.gif?imageMogr2/auto-orient/strip)



## 要求

* 需要 iOS 7 或更高版本，APP 样例是在 iOS 9  上创建的。

* 需要自动引用计数（ARC）。

* 针对 ARM64 架构进行了优化。


## 许可协议

查看[许可协议](https://github.com/Boris-Em/BEMCheckBox/blob/master/LICENSE)，你可以自由地进行更改，并将其用于个人或商业项目。 署名不是必要的，但我们高度赞赏（这样的行为）。 些许“谢谢！” （或有效的东西）总是受欢迎的。 如果你在应用程序中使用了 **BEMCheckBox** ，请让我们知道！

## 支持

如果你需要任何帮助或关于这个项目有任何讨论的话题请在  [Gitter](https://gitter.im/Boris-Em/BEMCheckBox) 上加入我们。

## App 样例

该项目附带的iOS APP样例演示了正确设置和使用 **BEMCheckBox** 的方法之一。 它还提供了在应用程序中自定义复选框的可能性。

## React Native 支持

**BEMCheckBox** 现在可以被使用于 **React Native** ：[React-Native-BEMCheckBox](https://github.com/torifat/react-native-bem-check-box)



# 开始使用

只需要几步就可以在你的项目中安装和创建 **BEMCheckBox**。

## 安装

* **CocoaPods**

  安装 **BEMCheckBox** 最容易的方式是使用  [CocoaPods](http://cocoapods.org/)。为此，只需将以下行添加到您的 ```Podfile``` :

  ```pod 'BEMCheckBox'```

> ⚠️如果使用 **CocoaPods** 将 **BEMCheckBox** 作为静态库导入项目，那么在 **Interface Builder** 中创建 **BEMCheckBox** 时会遇到这样的问题：
> [IB Designables: Failed to update auto layout status: Failed to load designables from path (null)](http://stackoverflow.com/questions/28204108/ib-designables-failed-to-update-auto-layout-status-failed-to-load-designables)
> 原因可能是这种方式不支持可视化设计。
> 解决方法：
* 以动态库的方式添加 **BEMCheckBox**, 使用 **CocoaPods** 时在 Podfile 文件中取消注释 ```use_frameworks!```,但是这样一来很多其他的第三方框架同时又会产生莫名其妙的问题。
* 使用 **Carthage**。
* 最简单的解决方法是手动把源文件拖进工程中。
* **Carthage**

  [Carthage](https://github.com/Carthage/Carthage) 是一个分散的依赖管理器，用于构建你的依赖关系并为你提供二进制框架。

  在你的 **Cartfile** 中添加 **BEMCheckBox** 以构建框架后运行 ```carthage update ```。 将内置的```BEMCheckBox.framework``` 拖到 **Xcode** 项目中。

* 手动导入

  最后，你可以手动添加 **BEMCheckBox**。只需将 Classes 文件夹拖放到 **Xcode** 项目中即可。当这样做时，确保并检查 ***“Copy items into destination group's folder”*** 框被选中。



## 创建

在你的项目中创建 **BEMCheckBox** 再简单不过了。它模仿 ```UISwitch```开关。事实上，你可以在项目中用 **BEMCheckBox** 替换 **UISwitch** 的实例！

以下是完成所有操作的步骤：

1. 导入 ```"BEMCheckBox.h"``` 到视图控制器的头文件中：

   ```objective-c
   import "BEMCheckBox.h"
   ```

2. **BEMCheckBox** 可以通过编程方式（通过代码）或 Interface Builder（Storyboard文件）进行初始化。 你可以使用对项目最有意义的方法。


**编程方式初始化**

只需要添加以下几行代码到你的 implementation 文件中（通常是在你视图控制器的  ```viewDidLoad``` 方法中）。

```objective-c
BEMCheckBox *myCheckBox = [[BEMCheckBox alloc] initWithFrame:CGRectMake(0, 0, 50, 50)];
[self.view addSubview:myCheckBox];
```



**Interface Builder 方式初始化**

1. 拖一个 ```UIView``` 到你的```UIViewController```中。
2. 将新的添加的```UIView```的类名改为```BEMCheckBox```。
3. 选择```BEMCheckBox```然后打开属性检视器 （Attributes Inspector）界面。大多数可定制的属性都能在这里被轻易地设置。APP示例应用演示了这个功能。




![](http://upload-images.jianshu.io/upload_images/2648731-3bfcd3acdba849a9.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



# 文档

**BEMCheckBox** 可用的所有方法和属性如下所示。



## 启用/禁用复选框

**属性** ：```on``` 

就像```UISwitch```一样，**BEMCheckBox** 提供了 **BOOL** 属性 ```on``` 来允许你检索和设置（无动画）一个值来确定**BEMCheckBox** 对象 ```on``` 或 ```off```。 默认为 ```NO```。

使用示例：

```objective-c
self.myCheckBox.on = YES;
```



**```setOn:animated:```**

就像```UISwitch```一样，**BEMCheckBox** 提供了实例方法 ```setOn:animated:```来设置复选框的状态为打卡或者关闭（可选切换动画）。

使用示例：

```objective-c
[self.myCheckBox setOn:YES animated:YES];
```



## 重新加载

实例方法 ```reload```允许你重绘整个复选框并且保持当前的 ```on``` 值。

使用示例：

```objective-c
[self.myCheckBox reload]
```



## 组/单选按钮功能

**BEMCheckBoxes** 可以轻松地被组合在一起形成单选按钮功能。 这会自动管理组中的每个复选框的状态，以便一次只选择一个，并且可以选择性地要求组中的按钮始终有一个被选择。

```objective-c
self.group = [BEMCheckBoxGroup groupWithCheckBoxes:@[self.checkBox1, self.checkBox2, self.checkBox3]];
self.group.selectedCheckBox = self.checkBox2; // 可选择设置哪个复选框被预选
self.group.mustHaveSelection = YES;           // 定义组中是否必须有一个被选择
```

要查看当前组按钮中哪一个复选框被选中，只要这样做：

```objective-c
BEMCheckBox *selection = self.group.selectedCheckBox;
```

要管理更新组按钮的选中状态，设置如下：

```objective-c
self.group.selectedCheckBox = self.checkBox1;
```



## Delegate 代理

**BEMCheckBox** 使用一个委托来接收复选框事件。 委托对象必须遵守 ```BEMCheckBoxDelegate``` 协议，该协议由两种可选方法组成：

* ```didTapCheckBox:```

  每当复选框被点击时调用。此方法将在属性更新（on）之后，动画完成之前（如果有的话）被触发。

* ```animationDidStopForCheckBox:```


  每次复选框完成动画时被调用以更新属性（on），并且动画完成后触发此方法。 如果没有动画启动，则不会被触发。




## 定制

**BEMCheckBox** 可通过属性专门定制。

下图提供了一个很好的概述：

![](http://upload-images.jianshu.io/upload_images/2648731-f2adc5b570fa73b2.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**外观属性**

```lineWidth``` - CGFloat

复选标记和框的线宽。 默认为2.0。



```hideBox``` - BOOL

BOOL 值来控制是否隐藏该复选框的盒子外壳。 将此属性设置为 `YES` 会将复选框变为如下的复选标记样式。 默认为 `No`。

[图片上传失败...(image-264717-1587703014156)]

```boxType``` - BEMBoxType

可用的不同复选框类型。查看 ```BEMBoxType``` 获得可用的值。默认为 ```BEMBoxTypeCircle```。

![](http://upload-images.jianshu.io/upload_images/2648731-73f0c3fe47a47f4d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`cornerRadius` - 矩形复选框时可以设置圆角属性。

```objective-c
/** The corner radius which is applied to the box when the boxType is BEMBoxTypeSquare. Default to 3.0.
 */
@property (nonatomic) IBInspectable CGFloat cornerRadius;
```



```tintColor``` - UIColor

复选框处于关闭时，框的颜色。



```onCheckColor``` - UIColor

复选框处于打开时，复选标记的颜色。



```onFillColor``` - UIColor

复选框处于打开时，框内的填充色。



```onTintColor``` - UIColor

复选框处于打开时，框周围线的颜色。



**动画**

```animationDuration``` - CGFloat

动画持续时间（以秒为单位）。 默认为```0.5```。



`onAnimationType` - BEMAnimationType

当选中复选框时使用的动画类型。 默认为```BEMAnimationTypeStroke```。 参见```BEMAnimationType```查看更多可用的类型。



`offAnimationType` - BEMAnimationType

当复选框被取消选择时使用的动画类型。 默认为```BEMAnimationTypeStroke```。 参见```BEMAnimationType```查看更多可用的类型。



`minimumTouchSize` - 最小触摸大小

```objective-c
/** If the checkbox width or height is smaller than this value, the touch area will be increased. Allows for visually small checkboxes to still be easily tapped. Default: (44, 44)
 */
@property (assign, nonatomic) IBInspectable CGSize minimumTouchSize;
```





`BEMAnimationType` - `onAnimationType` 和 `offAnimationType` 动画可用的值。

* `BEMAnimationTypeStroke` - **默认类型**

  ![](http://upload-images.jianshu.io/upload_images/2648731-5d30d92ce2c3b684.gif?imageMogr2/auto-orient/strip)

* `BEMAnimationTypeFill`

  ![](http://upload-images.jianshu.io/upload_images/2648731-4e8db5f4230b5270.gif?imageMogr2/auto-orient/strip)

* `BEMAnimationTypeBounce`
  ![](http://upload-images.jianshu.io/upload_images/2648731-50e20a693e6daf5f.gif?imageMogr2/auto-orient/strip)

* `BEMAnimationTypeFlat`
  ![](http://upload-images.jianshu.io/upload_images/2648731-7bd5a7f8e81b5243.gif?imageMogr2/auto-orient/strip)

* `BEMAnimationTypeOneStroke`
  ![](http://upload-images.jianshu.io/upload_images/2648731-f342c0e1a1f8e138.gif?imageMogr2/auto-orient/strip)

* `BEMAnimationTypeFade`
  ![](http://upload-images.jianshu.io/upload_images/2648731-ae1d09cca0949189.gif?imageMogr2/auto-orient/strip)






# 使用

## 示例一：

```objective-c
- (BEMCheckBox *)checkBox {
    if (!_checkBox) {
        _checkBox = [[BEMCheckBox alloc] initWithFrame:CGRectZero];
        // 矩形复选框
        _checkBox.boxType = BEMBoxTypeSquare;
        // 动画样式
        _checkBox.onAnimationType  = BEMAnimationTypeStroke;
        _checkBox.offAnimationType = BEMAnimationTypeStroke;
        _checkBox.animationDuration = 0.3;
        // 颜色样式
        _checkBox.tintColor    = [UIColor lightGrayColor];
        _checkBox.onTintColor  = HexColor(@"#108EE9");
        _checkBox.onFillColor  = [UIColor clearColor];
        _checkBox.onCheckColor = HexColor(@"#108EE9");
        // 默认选中
        _checkBox.on = YES;
    }
    return _checkBox;
}
```

## 示例一(2)
```
// Frameworks
#import <BEMCheckBox/BEMCheckBox.h>

@property (nonatomic, strong) BEMCheckBox *checkBox; // 记住密码按钮
@property (nonatomic, strong) UILabel *checkBoxLabel;

- (void)viewDidLoad {
    [super viewDidLoad];

    [self.view addSubview:self.checkBox];
    [self.view addSubview:self.checkBoxLabel];

    [self.checkBox mas_makeConstraints:^(MASConstraintMaker *make) {
        make.top.equalTo(self.background.mas_bottom).with.offset(padding);
        make.left.equalTo(self.view).with.offset(padding);
        make.size.mas_equalTo(CGSizeMake(21, 21));
    }];
    [self.checkBoxLabel mas_makeConstraints:^(MASConstraintMaker *make) {
        make.centerY.equalTo(self.checkBox.mas_centerY);
        make.left.equalTo(self.checkBox.mas_right).with.offset(8);
    }];

}

// 记住密码按钮
- (BEMCheckBox *)checkBox {
    if (!_checkBox) {
        _checkBox = [[BEMCheckBox alloc] initWithFrame:CGRectZero];
        _checkBox.boxType = BEMBoxTypeSquare;
        _checkBox.onAnimationType  = BEMAnimationTypeStroke;
        _checkBox.offAnimationType = BEMAnimationTypeStroke;
        _checkBox.animationDuration = 0.3;
        _checkBox.tintColor    = [UIColor lightGrayColor];
        _checkBox.onTintColor  = HexColor(@"#108EE9");
        _checkBox.onFillColor  = [UIColor clearColor];
        _checkBox.onCheckColor = HexColor(@"#108EE9");
        _checkBox.on = self.user.rememberPasswordState;
    }
    return _checkBox;
}

- (UILabel *)checkBoxLabel {
    if (!_checkBoxLabel) {
        _checkBoxLabel = [[UILabel alloc] initWithFrame:CGRectZero];
        NSDictionary *attributes = @{
                                     NSFontAttributeName:FONT(17),
                                     NSForegroundColorAttributeName:[UIColor lightGrayColor]
                                     };
        NSAttributedString *text = [[NSAttributedString alloc] initWithString:@"保持登录状态"
                                                                   attributes:attributes];
        [_checkBoxLabel setAttributedText:text];
        [_checkBoxLabel setBackgroundColor:[UIColor clearColor]];
    }
    return _checkBoxLabel;
```

## 示例二：

```objective-c
// 使用 Interface Builder 设置复选框时，可能还需要如下设置
self.checkBox21.onAnimationType  = BEMAnimationTypeFade;
self.checkBox21.offAnimationType = BEMAnimationTypeFade;
self.checkBox22.onAnimationType  = BEMAnimationTypeFade;
self.checkBox22.offAnimationType = BEMAnimationTypeFade;

NSArray *checkBoxArray2 = @[self.checkBox21,self.checkBox22];
self.checkBoxGroup2 = [BEMCheckBoxGroup groupWithCheckBoxes:checkBoxArray2];
self.checkBoxGroup2.selectedCheckBox = self.checkBox21;
self.checkBoxGroup2.mustHaveSelection = YES;
```
### 示例三：

一组单选框按钮完整示例：

```objective-c
// Frameworks
#import <BEMCheckBox/BEMCheckBox.h>

@interface HQLInsurancePaymentViewController () <UITextFieldDelegate, BEMCheckBoxDelegate>
  
@property (nonatomic, strong) BEMCheckBoxGroup *checkBoxGroup;
@property (nonatomic, strong) BEMCheckBox *checkBox1;
@property (nonatomic, strong) BEMCheckBox *checkBox2;

@end
  
@implementation HQLInsurancePaymentViewController

#pragma mark - Lifecycle

- (void)viewDidLoad {
    [super viewDidLoad];
    [self addSubviews];
    [self setupBEMCheckBoxGroup];
}

#pragma mark - Custom Accessors

- (BEMCheckBox *)checkBox1 {
    if (!_checkBox1) {
        _checkBox1 = [[BEMCheckBox alloc] initWithFrame:CGRectZero];
        _checkBox1.onAnimationType = BEMAnimationTypeBounce;
        _checkBox1.offAnimationType = BEMAnimationTypeBounce;
        _checkBox1.animationDuration = 0.3;
        _checkBox1.delegate = self;
    }
    return _checkBox1;
}

- (BEMCheckBox *)checkBox2 {
    if (!_checkBox2) {
        _checkBox2 = [[BEMCheckBox alloc] initWithFrame:CGRectZero];
        _checkBox2.onAnimationType = BEMAnimationTypeBounce;
        _checkBox2.offAnimationType = BEMAnimationTypeBounce;
        _checkBox1.animationDuration = 0.3;
        _checkBox2.delegate = self;
    }
    return _checkBox2;
}

#pragma mark - Private

- (void)addSubviews {
    self.view.backgroundColor = [UIColor whiteColor];
    [self.view addSubview:self.checkBox1];
    [self.view addSubview:self.checkBox2];
  
    UILabel *title = [[UILabel alloc] init];
    title.text = @"1.请选择支付方式";
    title.textColor = HexColor(@"#3E91FF");
    title.font = [UIFont systemFontOfSize:17 weight:UIFontWeightMedium];
    [self.view addSubview:title];
  
    [self.checkBox1 mas_makeConstraints:^(MASConstraintMaker *make) {
        make.top.equalTo(title.mas_bottom).with.offset(padding);
        make.left.equalTo(title.mas_left).with.offset(16);
        make.size.mas_equalTo(CGSizeMake(21, 21));
    }];
    [self.checkBox2 mas_makeConstraints:^(MASConstraintMaker *make) {
        make.top.equalTo(self.checkBox1.mas_bottom).with.offset(15);
        make.left.equalTo(self.checkBox1);
        make.size.mas_equalTo(CGSizeMake(21, 21));
    }];
}

- (void)setupBEMCheckBoxGroup {
    NSArray *checkBoxArray = @[self.checkBox1,self.checkBox2];
    self.checkBoxGroup = [BEMCheckBoxGroup groupWithCheckBoxes:checkBoxArray];
    self.checkBoxGroup.selectedCheckBox = self.checkBox1;
    self.checkBoxGroup.mustHaveSelection = YES;
}

#pragma mark - BEMCheckBoxDelegate

- (void)didTapCheckBox:(BEMCheckBox *)checkBox {
    [self.view endEditing:YES];
}
```

![示例三](http://upload-images.jianshu.io/upload_images/2648731-b2299796508dac39.gif?imageMogr2/auto-orient/strip)
