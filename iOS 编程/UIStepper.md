# UIStepper 步进器

![](http://upload-images.jianshu.io/upload_images/2648731-b6edf90c8e543fb6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



步进器是一个用于增加或减少数值的两段控件。默认状态下，步进器的一个分段显示一个加号（+）而另外一个显示减号（-）。如果有需要，这些符号也可以替换成自定义图片。

**确保步进器所调整的数值显眼易见。**由于步进器本身不显示任何值，因此要确保用户知道他们用步进器改变的是什么值。

**若可能涉及到较大数值的调整，那就不要使用步进器。**步进器适用于只需少量点击的小数值调整。比如，在打印页面，使用步进器调整打印份数是合理的，因为用户极少会大幅度调整这个值。反之，用步进器来选择页码范围就是不合理的，因为即使是对于页码不多的文档，用户也必须通过多次点击才能完成选择。

了解开发详情，请参阅[UIStepper](https://developer.apple.com/reference/uikit/uistepper)。



## 预览

如果将步进器行为设置为 `autorepeat`（这是默认值），则按住其中一个按钮会持续增加或减少步进器的值。 变化率取决于用户持续按压控制的时间。
最大值必须大于等于最小值。 如果设置了一个会导致步进中断的最大值或最小值，这两个值都将设置为新值。 例如，如果最小值为200，并设置最大值为100，则最小值和最大值都为200。



## 主题

### 配置步进器

* `continuous`

  步进器的连续与非连续状态。代表被改变的值是在用户交互期间立即更改，还是用户交互结束时再更改？
  此属性的默认值为YES。

* `autorepeat`

  步进器的自动与非自动步进状态。代表用户持续按压会不会自动持续增加或减少步进器的值。

* `wraps`

  步进器的包装与非包装状态。

  > 如果设置为 **YES**，增加超过 `maximumValue` 将值设置为 `minimumValue` ; 同样，减少到 `minimumValue` 以下将值设置为 `maximumValue`。 如果设置为 NO，步进器不会超出`maximumValue`，也不会递减到 `minimumValue` 以下，而是保持在这些值。此属性的默认值为 **NO**。

* `minimumValue` 

  最小值。

* `maximumValue`

  最大值。

* `stepValue`

  步进值，默认为1。

### 获取步进值

* `value`

  步进器的数值。

  > 当值更改时，步进器将 **UIControlEventValueChanged** 标志发送到其 Target（参阅 `addTarget：action：forControlEvents：`）。 关于值更改事件是连续发送还是用户交互结束时发送，请参阅 `continuous` 属性。
  > 此属性的默认值为0。此属性在其下限处被钳制至 `minimumValue`，并将其上限钳制到`maximumValue`。

### 配置外观

* `tintColor`

* `backgroundImageForState:`

  获取步进器在不同状态下的背景图片。

* `setBackgroundImage:forState:`

  设置步进器在不同状态下的背景图片。

* `decrementImageForState:`

  返回用于控件的递减字形的图像。(把步进器的减号（“-”）替换为图片)

* `setDecrementImage:forState:`

  设置递减值的图片。

* `dividerImageForLeftSegmentState:rightSegmentState:`

  返回给定的左右状态组合的分割图像。

* `setDividerImage:forLeftSegmentState:rightSegmentState:`

  设置分割图像。

* `incrementImageForState:`

  返回用于控件的递增字形的图像。(把步进器的加号（“+”）替换为图片)。

* `setIncrementImage:forState:`

  设置用于控件的递增字形的图像。



## 使用 UIStepper 

### 示例代码：

```objective-c
@implementation ViewController

#pragma mark - Lifecycle

- (void)viewDidLoad {
    [super viewDidLoad];
    
    // 标签，用于显示步进值
    UILabel *label = [[UILabel alloc] initWithFrame:CGRectMake(35, 100, 105, 29)];
    label.backgroundColor = [UIColor flatWhiteColor];
    label.textColor = [UIColor flatBlueColor];
    label.textAlignment = NSTextAlignmentCenter;
    self.label = label;
    [self.view addSubview:self.label];
    
    // 创建 UIStepper 实例对象
    UIStepper *stepper = [[UIStepper alloc] initWithFrame:CGRectMake(150, 100, 94, 29)];
    // 用户交互时实时更新步进值
    stepper.continuous = YES; // default = YES
    // 用户持续按住，自动增加或减少
    stepper.autorepeat = YES; // default = YES
    // 如果设置为 YES, 值会在 min <-> max 之间循环
    stepper.wraps = NO; // default = NO
    stepper.minimumValue = 0;   // default 0
    stepper.maximumValue = 100; // default 100
    stepper.stepValue    = 1;   // default 1
    // 设置 Target-Action
    [stepper addTarget:self
                action:@selector(stepperValueChanged:)
      forControlEvents:UIControlEventValueChanged];
    // 添加到视图
    [self.view addSubview:stepper];
}

- (void)stepperValueChanged:(UIStepper *)stepper {
    // 获取步进值
    NSString *value = [NSString stringWithFormat:@"%f",stepper.value];
    self.label.text = value;
}
```

### 效果

![](http://upload-images.jianshu.io/upload_images/2648731-d3349d1fbc4988b8.gif?imageMogr2/auto-orient/strip)



### 参考设置大小

![](http://upload-images.jianshu.io/upload_images/2648731-9bf85052875d40a3.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)





### 参考

* [UIStepper API](https://developer.apple.com/reference/uikit/uistepper)


* [译文 | iOS 10 人机界面指南 （完结）](http://www.woshipm.com/ucd/639614.html)
