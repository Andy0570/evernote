* GitHub源码：[JVFloatLabeledTextField](https://github.com/jverdi/JVFloatLabeledTextField)
* star: 6000+

>                                 ⭐️⭐️⭐️ 
以下内容来源于官方源码、 README 文档、测试 Demo或个人使用总结 ！

![](http://upload-images.jianshu.io/upload_images/2648731-6ce22e86b4b3d764?imageMogr2/auto-orient/strip)

> **JVFloatLabeledTextField** 是已经被称为“浮动标签模式”的UX模式的第一实现。

> 由于移动设备的空间限制，通常只依靠占位符作为标记字段的手段。 这就会产生UX问题，因为一旦用户开始填写表单，就不会出现占位符标签。

> 这个包含 **UITextField** 和 **UITextView** 子类的UI组件库旨在通过将占位符转换为悬停在浮动标签之上的浮动标签来改善用户体验。

>可以有效解决移动设备上进行表单输入时,无法边看表单提示边进行输入的困境。

以下是两个头文件的源码：
### JVFloatLabeledTextField.h
```
IB_DESIGNABLE
@interface JVFloatLabeledTextField : UITextField

/**
 * 只读的浮动式标签.
 */
@property (nonatomic, strong, readonly) UILabel * floatingLabel;

/**
 * 浮动标签距离内容的垂直距离(Y坐标).
 * 默认是 0.
 */
@property (nonatomic) IBInspectable CGFloat floatingLabelYPadding;

/**
 * 浮动标签距离内容的水平距离(X坐标).
 * 默认是 0.
 */
@property (nonatomic) IBInspectable CGFloat floatingLabelXPadding;

/**
 * 用于修改浮动标签字体大小的比例.
 * 默认是 70%.
 */
@property (nonatomic) IBInspectable CGFloat floatingLabelReductionRatio;

/**
 * 占位符内容的垂直距离(Y坐标).
 * 默认是 0.
 */
@property (nonatomic) IBInspectable CGFloat placeholderYPadding;

/**
 * 浮动式标签的字体.
 * 默认值有两个,按优先顺序排列:
 * - 自定义的占位符内容字体的70%.
 * - 自定义的输入框内容字体的70%.
 */
@property (nonatomic, strong) UIFont * floatingLabelFont;

/**
 * 浮动式标签的正常字体颜色.
 * 默认是 [UIColor grayColor].
 */
@property (nonatomic, strong) IBInspectable UIColor * floatingLabelTextColor;

/**
 * 输入框成为第一响应者时,浮动标签的文字颜色.
 * 如未设置，默认是 Tint color.
 */
@property (nonatomic, strong) IBInspectable UIColor * floatingLabelActiveTextColor;

/**
 * 指明浮动式标签的外观是否只在输入框成为第一响应者时才使用动态变化效果.
 * 默认情况下,仅在输入框成为第一响应者时,浮动式标签的外观才会有动态变化的效果.
 */
@property (nonatomic, assign) IBInspectable BOOL animateEvenIfNotFirstResponder;

/**
 * 显示浮动式标签的动画时长.
 * 默认0.3秒.
 */
@property (nonatomic, assign) NSTimeInterval floatingLabelShowAnimationDuration;

/**
 * 隐藏浮动式标签时的动画时长.
 * 默认0.3秒.
 */
@property (nonatomic, assign) NSTimeInterval floatingLabelHideAnimationDuration;

/**
 * 指明清除按钮是否自动和文本对齐.
 * 默认值:YES.
 */
@property (nonatomic, assign) IBInspectable BOOL adjustsClearButtonRect;

/**
 * 指明当输入文字时,是否下调基准线(baseline).设置为YES(非默认值),意味着占位内容会和输入内容对齐.
 * 默认为 NO（标准占位符内容将高于任何输入的文本）
 */
@property (nonatomic, assign) IBInspectable BOOL keepBaseline;

/**
 * 强制浮动式标签始终可见.
 * 默认为 NO.
 */
@property (nonatomic, assign) BOOL alwaysShowFloatingLabel;

/**
 * 占位符文本的颜色
 */
@property (nonatomic, strong) IBInspectable UIColor * placeholderColor;

/**
 *  设置占位符文字和浮动式标签的文字.
 *
 *  @param placeholder 没有文字输入时,默认显示的内容.
 *  @param floatingTitle 当用户输入文本后，文本字段上方显示的浮动式标签字符串。
 */
- (void)setPlaceholder:(NSString *)placeholder floatingTitle:(NSString *)floatingTitle;

/**
 *  设置 attributed 占位符文字和浮动式标签的文字.
 *
 *  @param attributedPlaceholder 没有文字输入时,默认显示的内容.
 *  @param floatingTitle 当用户输入文本后，文本字段上方显示的浮动式标签字符串。
 */
- (void)setAttributedPlaceholder:(NSAttributedString *)attributedPlaceholder floatingTitle:(NSString *)floatingTitle;
```

### JVFloatLabeledTextView.h
```
IB_DESIGNABLE
@interface JVFloatLabeledTextView : UITextView

/**
 * 当没有其他文本存在时，在文本视图中显示的占位符字符串。
 */
@property (nonatomic, copy) IBInspectable NSString * placeholder;

/**
 * 只读属性的占位符文本框
 */
@property (nonatomic, strong, readonly) UILabel * placeholderLabel;

/**
 * 只读属性的浮动式标签文本框
 */
@property (nonatomic, strong, readonly) UILabel * floatingLabel;

/**
 * 浮动标签距离内容的垂直距离(Y坐标).
 */
@property (nonatomic) IBInspectable CGFloat floatingLabelYPadding;

/**
 * 浮动标签距离内容的水平距离(X坐标).
 */
@property (nonatomic) IBInspectable CGFloat floatingLabelXPadding;

/**
 * 占位符内容的垂直距离(Y坐标).
 */
@property (nonatomic) IBInspectable CGFloat placeholderYPadding;

/**
 * 浮动标签文本的字体. 默认 [UIFont boldSystemFontOfSize:12.0f].
 * 提供方便的外观代理方法
 */
@property (nonatomic, strong) UIFont * floatingLabelFont;

/**
 * 浮动式标签的正常字体颜色.
 * 默认 [UIColor grayColor].
 * 提供方便的外观代理方法
 */
@property (nonatomic, strong) IBInspectable UIColor * floatingLabelTextColor;

/**
 * 输入框成为第一响应者时,浮动标签的文字颜色.
 * 如未设置，默认是 Tint color.
 */
@property (nonatomic, strong) IBInspectable UIColor * floatingLabelActiveTextColor;

/**
 * 浮动标签是否应锁定到文本视图的顶部，或者当文本视图是可滚动的时，可以使浮动标签跟随文本滚动。 默认情况下，浮动标签将锁定到文本视图的顶部，并将其背景颜色设置为文本视图的背景颜色
 * 请注意，当浮动标签具有非清晰的背景颜色时，效果最佳。
 */
@property (nonatomic, assign) IBInspectable BOOL floatingLabelShouldLockToTop;

/**
 * 占位符文本的字体颜色.
 * 默认 [[UIColor lightGrayColor] colorWithAlphaComponent:0.65f].
 */
@property (nonatomic, strong) IBInspectable UIColor * placeholderTextColor;

/**
 * 指明浮动式标签的外观是否只在输入框成为第一响应者时才使用动态变化效果.
 * 默认情况下,仅在输入框成为第一响应者时,浮动式标签的外观才会有动态变化的效果.
 */
@property (nonatomic, assign) IBInspectable BOOL animateEvenIfNotFirstResponder;

/**
 * 显示浮动式标签的动画时长.
 * 默认0.3秒.
 */
@property (nonatomic, assign) NSTimeInterval floatingLabelShowAnimationDuration UI_APPEARANCE_SELECTOR;

/**
 * 隐藏浮动式标签时的动画时长. 
 * 默认0.3秒.
 */
@property (nonatomic, assign) NSTimeInterval floatingLabelHideAnimationDuration UI_APPEARANCE_SELECTOR;

/**
 * 强制浮动式标签始终可见.
 * 默认 NO
 */
@property (nonatomic, assign) BOOL alwaysShowFloatingLabel;

/**
 * 文本容器插入的顶部值
 * 如果你需要在文本输入框和浮动标签之间有更多填充空间，请更改此值
 */
@property (nonatomic) CGFloat startingTextContainerInsetTop;

/**
 *  设置占位符文字和浮动式标签的文字.
 *
 *  @param placeholder 没有文字输入时,默认显示的内容.
 *  @param floatingTitle 当用户输入文本后，文本字段上方显示的浮动式标签字符串.
 */
- (void)setPlaceholder:(NSString *)placeholder floatingTitle:(NSString *)floatingTitle;
```

### 使用

```
- (JVFloatLabeledTextField *)idNumberTextField {
    if (!_idNumberTextField) {
        _idNumberTextField = [[JVFloatLabeledTextField alloc] initWithFrame:CGRectZero];
        _idNumberTextField.font = FONT_THEME;
        // 浮动式标签的正常字体颜色
        _idNumberTextField.floatingLabelTextColor = COLOR_THEME;
        // 输入框成为第一响应者时,浮动标签的文字颜色.
        _idNumberTextField.floatingLabelActiveTextColor = [UIColor blueColor];
        // 指明当输入文字时,是否下调基准线(baseline).设置为YES(非默认值),意味着占位内容会和输入内容对齐.
        _idNumberTextField.keepBaseline = YES;
        // 设置占位符文字和浮动式标签的文字.
        [_idNumberTextField setPlaceholder:@"请输入身份证号码"
                             floatingTitle:@"身份证号码"];
        _idNumberTextField.clearButtonMode = UITextFieldViewModeWhileEditing;
        _idNumberTextField.delegate = self;
    }
    return _idNumberTextField;
}
```

### XIB问题

在Xcode 8 环境下使用 **Cocoapods** 导入该框架，使用xib文件创建TextField就会报错：
```
IB Designables: Failed to update auto layout status: Failed to load designables from path (null)
IB Designables: Failed to render instance of TTTAttributedLabel: Failed to load designables from path (null)
```

* 参考[TTTAttributedLabel](https://cocoapods.org/pods/TTTAttributedLabel)的文档，如果想要用 xib 方式创建TextField，就不能用**Cocoapods** 将其作为静态库导入项目中，解决方式是：下载源码后手动拖进工程里使用。


###参考
* [JVFloatLabeledTextField：用户体验优先的浮动式表单输入框](http://hao.jobbole.com/jvfloatlabeledtextfield-ios/)
