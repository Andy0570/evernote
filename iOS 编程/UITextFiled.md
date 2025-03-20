`UITextField` 对象用于在你的界面上显示一个可编辑的文本区域。使用文本区域可以收集来自用户使用屏幕上的键盘输入的基于文本的信息。键盘可以被配置为不同的输入类型，如纯文本，电子邮件，数字等等。文本区域使用 Target-Action 机制和委托对象来报告在编辑过程中所做的更改。

### 初始化 `UITextFiled` ，设置位置及大小

```objc
CGRect textFiledRect = CGRectMake(87, 70, 240, 30);
UITextField *textFiled = [[UITextField alloc] initWithFrame:textFiledRect];
```

### 设置 `UITextFiled` 对象的边框样式

```objc
textFiled.borderStyle = UITextBorderStyleRoundedRect;
```

如果设置为 `UITextBorderStyleRoundedRect`，自定义背景图像被忽略。

样式类型：

* `UITextBorderStyleNone`
* `UITextBorderStyleLine`
* `UITextBorderStyleBezel`
* `UITextBorderStyleRoundedRect`
  
### 设置输入框的背景颜色

```objc
textFiled.backgroundColor = [UIColor whiteColor];
```

### 设置背景
> [!CAUTION]
>
> 只有在 `UITextBorderStyleNone` 样式下，设置背景图才会生效，且图片大小小于 `text` 的 `frame` 时，图片会拉伸。

```objc
textFiled.borderStyle = UITextBorderStyleNone;
textFiled.background = [UIImage imageNamed:@"***.png"];
```

### 设置 `enable` 值为 `NO` 时的背景

```objc
textFiled.disabledBackground = [UIImage imageNamed:@"***.png"];
```

### 当输入框无内容时，设置占位符提示

```objc
textFiled.placeholder = @"Hypontize me";
```

### 设置输入框默认文本值

```objc
textFiled.text = @"默认值";
```

### 设置输入框内容的字体样式和大小

```objc
textFiled.font = [UIFont fontWithName:@"Arial" size:16];
```

### 设置字体颜色

```objc
textFiled.textColor = [UIColor blueColor];
```

### 输入框中是否显示×号，用于清空输入框内容

```objc
textFiled.clearButtonMode = UITextFieldViewModeAlways;
```

样式类型：

* `UITextFieldViewModeNever`：从不出现
* `UITextFieldViewModeWhileEditing`： 编辑时出现
* `UITextFieldViewModeUnlessEditing`： 编辑外出现
* `UITextFieldViewModeAlways`：一直出现


### 用于密码输入：每输入一个字符就变成 `*`

```objc
textFiled.secureTextEntry = YES;
```

### 自动大小写功能

```objc
textFiled.autocapitalizationType = UITextAutocapitalizationTypeWords;
```

样式类型：
*  `UITextAutocapitalizationTypeNone`：关闭
* `UITextAutocapitalizationTypeWords`：单词
* `UITextAutocapitalizationTypeSentences`：句子
* `UITextAutocapitalizationTypeAllCharacters`：所有字母

### 换行键自动监测功能

如果将该属性设置为 `YES`，`UITextField` 对象会自动监测用户输入，并根据是否输入了文字启用/禁用换行键,如果文本内容长度为0，则禁用换行键，如果文本内容长度不为0，则启用换行键.

```objc
textFiled.enablesReturnKeyAutomatically = YES;
```

### 拼写建议

可以提供用户输错的单词并提供修改建议：

```objc
textFiled.autocorrectionType = UITextAutocorrectionTypeYes;
```

*样式类型：*

* `UITextAutocorrectionTypeDefault`
* `UITextAutocorrectionTypeNo`
* `UITextAutocorrectionTypeYes`

### 再次编辑就清空
```objc
textFiled.clearsOnBeginEditing = YES;
```

### 内容对齐方式
```objc
textFiled.textAlignment =  NSTextAlignmentCenter;
```

*样式类型：*

* NSTextAlignmentLeft      = 0,    // 自视左对齐
* NSTextAlignmentCenter    = 1,    // 居中
* NSTextAlignmentRight     = 2,    // 右对齐   
  
### 内容的垂直对齐方式
```objc
textFiled.contentVerticalAlignment = UIControlContentVerticalAlignmentCenter;
```

*样式类型：*

* UIControlContentVerticalAlignmentCenter  = 0
* UIControlContentVerticalAlignmentTop     = 1
* UIControlContentVerticalAlignmentBottom  = 2
* UIControlContentVerticalAlignmentFill    = 3
  
### 文本自适应文本窗口大小
默认是保持原来大小,而让长文本滚动

```objc
textFiled.adjustsFontSizeToFitWidth = YES;
// 设置文本最小字体，adjustsFontSizeToFitWidth=YES 时才起作用
textFiled.minimumFontSize = 10;
```

### 设置键盘样式
```objc
textFiled.keyboardType = UIKeyboardTypeDefault;
```

*样式类型：*

* UIKeyboardTypeDefault,                // 默认键盘，支持所有字符
* UIKeyboardTypeASCIICapable,           // 支持ASCII的默认键盘
* UIKeyboardTypeNumbersAndPunctuation,  // 标准电话键盘，支持＋＊＃字符
* UIKeyboardTypeURL,                    // URL键盘，支持.com按钮 只支持URL字符
* UIKeyboardTypeNumberPad,              // 数字键盘
* UIKeyboardTypePhonePad,               // 电话键盘
* UIKeyboardTypeNamePhonePad,           // 电话键盘，也支持输入人名
* UIKeyboardTypeEmailAddress,           // 用于输入电子邮件地址的键盘
* UIKeyboardTypeDecimalPad NS_ENUM_AVAILABLE_IOS(4_1),   // 数字键盘 有数字和小数点
* UIKeyboardTypeTwitter NS_ENUM_AVAILABLE_IOS(5_0),      // 优化的键盘，方便输入@、#字符，用于twitter文本输入
* UIKeyboardTypeWebSearch NS_ENUM_AVAILABLE_IOS(7_0),    // 支持添加链接的默认键盘类型
* UIKeyboardTypeAlphabet = UIKeyboardTypeASCIICapable, // Deprecated

样式参考：[UIKeyboardType 图解](http://www.jianshu.com/p/446768b350c6)

### 软键盘换行键默认显示值

```objc
textFiled.returnKeyType = UIReturnKeyDone;
```

*样式类型：*

* UIReturnKeyDefault,   默认灰色按钮，标有Return
* UIReturnKeyGo,        标有Go 的蓝色按钮
* UIReturnKeyGoogle,    标有 Google 的蓝色按钮，用于搜索
* UIReturnKeyJoin,      标有Join 的蓝色按钮
* UIReturnKeyNext,      标有 Next 的蓝色按钮
* UIReturnKeyRoute,     标有 Route 的蓝色按钮
* UIReturnKeySearch,    标有 Search 的蓝色按钮
* UIReturnKeySend,      标有 Send 的蓝色按钮
* UIReturnKeyYahoo,     标有 Yahoo 的蓝色按钮
* UIReturnKeyDone,      标有 Done 的蓝色按钮
* UIReturnKeyEmergencyCall,     紧急呼叫按钮
* UIReturnKeyContinue NS_ENUM_AVAILABLE_IOS(9_0),   标有 Continue 的蓝色按钮

    
### 键盘外观
```objc
textFiled.keyboardAppearance = UIKeyboardAppearanceAlert;
```

*样式类型：*

* UIKeyboardAppearanceDefault,          // 默认外观
* UIKeyboardAppearanceDark NS_ENUM_AVAILABLE_IOS(7_0),
* UIKeyboardAppearanceLight NS_ENUM_AVAILABLE_IOS(7_0),
* UIKeyboardAppearanceAlert = UIKeyboardAppearanceDark,  // Deprecated

    
### 最右侧加图片
```objc
UIImageView *image = [[UIImageView alloc]initWithImage:[UIImage imageNamed:@"***.png"]];
textFiled.rightView = image;
textFiled.rightViewMode = UITextFieldViewModeAlways;
```

### 设置左侧图片

```objc
// 设置左侧图片
self.textField1.leftView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, CGRectGetHeight(self.textField1.frame), CGRectGetHeight(self.textField1.frame))];
self.textField1.leftViewMode = UITextFieldViewModeAlways;

UIImageView* imgUser = [[UIImageView alloc] initWithFrame:CGRectMake(9, 9, 22, 22)];
imgUser.image = [UIImage imageNamed:@"iconfont-user"];
[self.textField1.leftView addSubview:imgUser];
```

*样式类型：*

* UITextFieldViewModeNever,
* UITextFieldViewModeWhileEditing,
* UITextFieldViewModeUnlessEditing,
* UITextFieldViewModeAlways
  
### 把 textFiled 添加到视图中
```objc
[backgroundView addSubview:textFiled];
```

### 按 return 键盘放弃第一响应者
`UITextfield` 实例要遵守```UITextFieldDelegate```协议：

```objc
//设置代理，用于实现协议
//声明textFiled的代理是我，我会去实现把键盘往下收的方法 这个方法在UITextFieldDelegate里所以我们要采用UITextFieldDelegate这个协议
textFiled.delegate = self;

- (BOOL)textFieldShouldReturn:(UITextField *)textField
{
    [textField resignFirstResponder];    
    return YES;
}
```

### 重写绘制行为
除了```UITextField```对象的风格选项，你还可以定制化```UITextField```对象，为他添加许多不同的重写方法，来改变文本字段的显示行为。这些方法都会返回一个```CGRect```结构，制定了文本字段每个部件的边界范围。以下方法都可以重写。

``` objc
– textRectForBounds:　　  　//重写来重置文字区域
– drawTextInRect:　　      　//改变绘文字属性.重写时调用super可以按默认图形属性绘制,若自己完全重写绘制函数，就不用调用super了.
– placeholderRectForBounds:　　//重写来重置占位符区域
– drawPlaceholderInRect:　　//重写改变绘制占位符属性.重写时调用super可以按默认图形属性绘制,若自己完全重写绘制函数，就不用调用super了.
– borderRectForBounds:　　//重写来重置边缘区域
– editingRectForBounds:　　//重写来重置编辑区域
– clearButtonRectForBounds:　　//重写来重置clearButton位置,改变size可能导致button的图片失真
– leftViewRectForBounds:
– rightViewRectForBounds:
```
### 委托方法
1. **目标-动作（Target-Action）设计模式：**当某个特定的事件发生时（例如按下按钮），发生事件的一方会向指定的目标对象发送一个之前设定好的动作消息。
2. 在目标-动作中，针对不同的事件，需要创建不同的动作消息。**UIButton** 对象的事件比较简单，通常只需要处理点击事件；相反，像 **UITextField**这类事件复杂的对象，Apple使用委托设计模式。 **UITextField**对象具有一个委托属性，通过为 **UITextField**对象设置委托， **UITextField**对象会在发生事件时向委托发送响应的消息，由委托处理该事件。
3. 凡是支持委托的对象，其背后都有一个相应的协议，如果一个类实现了某个协议中规定的方法，就称这个类遵守（conform）了该协议。

```objc
//UITextFieldDelegate协议
@protocol UITextFieldDelegate <NSObject>
```
```objc
@optional   //写在该指令之后的所有方法全部声明为可选的
@required  //必须实现的方法
```
对于编辑 UITextField 对象文本内容的事件，有以下两个对应的委托方法：


```objc
// 开始编辑时触发，文本字段将成为first responder
- (void)textFieldDidBeginEditing:(UITextField *)textField;

// 该方法被触发时，输入框已经结束编辑状态
- (void)textFieldDidEndEditing:(UITextField *)textField;             
// may be called if forced even if shouldEndEditing returns NO (e.g. view removed from window) or endEditing:YES called
```

还有一类带有返回值的委托方法，用于从委托中查询需要的信息

```objc
 - BOOL)textFieldShouldBeginEditing:(UITextField *)textField {
（输入框询问是否可以编辑，YES可以编辑，NO不能编辑）
 //返回一个BOOL值，指定是否循序文本字段开始编辑,返回NO时不能唤起键盘进行编辑。
   return YES;
}
 
 - (BOOL)textFieldShouldEndEditing:(UITextField *)textField {
  //返回BOOL值，指定是否允许文本字段结束编辑，当编辑结束，文本字段会放弃first responder状态　
  //要想在用户结束编辑时阻止文本字段消失，可以返回NO　
  //这对一些文本字段必须始终保持活跃状态的程序很有用，比如即时消息
  return NO;　//一直处于编辑状态
 }

 - (BOOL)textField:(UITextField *)textField shouldChangeCharactersInRange:(NSRange)range replacementString:(NSString *)string {

	//当用户使用自动更正功能，把输入的文字修改为推荐的文字时，就会调用这个方法。　
	//这对于想要加入撤销选项的应用程序特别有用　
	//可以跟踪字段内所做的最后一次修改，也可以对所有编辑做日志记录,用作审计用途。　　 
	//要防止文字被改变可以返回NO　
	//这个方法的参数中有一个NSRange对象，指明了被改变文字的位置，建议修改的文本也在其中　
	return YES;
}
 
 - (BOOL)textFieldShouldClear:(UITextField *)textField {
//返回一个BOOL值指明是否允许根据用户请求清除内容　
//可以设置在特定条件下才允许清除内容　
return YES;　//返回YES，输入内容后，点击右边的清除按钮可以清除，返回NO，点击清除不起作用	
}

 - (BOOL)textFieldShouldReturn:(UITextField *)textField {

	//返回一个BOOL值，指明是否允许在按下回车键时结束编辑　
	//如果允许要调用resignFirstResponder 方法，这会导致结束编辑，而键盘会被收起[textField 	resignFirstResponder];
	//查一下resign（放弃）这个单词的意思就明白这个方法了　
	return YES;
}
```

例如 **UITextField** 对象的委托属性为 **HQLHyponsisViewController**自身，**HQLHyponsisViewController**类实现了 **UITextFieldDelegate**协议，当“return”键被按下时触发协议方法， **HQLHyponsisViewController.m** 文件中实现的``textFieldShouldReturn:``方法被调用。


### 通知

`UITextField` 派生自 `UIControl`，所以 `UIControl` 类中的通知系统在文本字段中也可以使用。除了`UIControl` 类的标准事件，你还可以使用下列 `UITextField` 类特有的事件。

只有这三个：

* `UITextFieldTextDidBeginEditingNotification `
* `UITextFieldTextDidChangeNotification `
* `UITextFieldTextDidEndEditingNotification `

当文本字段退出编辑模式时触发。通知的 `object` 属性存储了最终文本。 
因为文本字段要使用键盘输入文字，所以下面这些事件发生时，也会发送动作通知.

```objc
UIKeyboardWillShowNotification 　//键盘显示之前发送
UIKeyboardDidShowNotification  　//键盘显示之后发送
UIKeyboardWillHideNotification 　//键盘隐藏之前发送
UIKeyboardDidHideNotification  　//键盘隐藏之后发送
```

#### 监听 `UITextField` 的值判断是否禁用按钮

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    // 添加通知
    [[NSNotificationCenter defaultCenter] addObserver:self
                                             selector:@selector(textFieldTextDidChanged:)
                                                 name:UITextFieldTextDidChangeNotification
                                               object:nil];
}

- (void)dealloc {
    // 移除通知
    [[NSNotificationCenter defaultCenter] removeObserver:self];
}

#pragma mark - NSNotification

- (void)textFieldTextDidChanged:(NSNotification *)notification {
    BOOL isTextFieldTextNull = (self.usernameTextField.text.length == 0)||(self.passwordTextField.text.length == 0) ;
    self.loginButton.enabled = !isTextFieldTextNull;
}
```


### 在 storyboard 中设置 `UITextField`

1. Text ：设置文本框的默认文本。
2. Placeholder ： 可以在文本框中显示灰色的字，用于提示用户应该在这个文本框输入什么内容。当这个文本框中输入了数据时，用于提示的灰色的字将会自动消失。
1. Background ：
1. Disabled ： 若选中此项，用户将不能更改文本框内容。
1. 接下来是三个按钮，用来设置对齐方式。
1. Border Style ： 选择边界风格。
1. Clear Button ： 这是一个下拉菜单，你可以选择清除按钮什么时候出现，所谓清除按钮就是出一个现在文本框右边的小 X ，你可以有以下选择：
    * Never appears ： 从不出现
    * Appears while editing ： 编辑时出现
    * Appears unless editing ： 
    * Is always visible ： 总是可见
1. Clear when editing begins ： 若选中此项，则当开始编辑这个文本框时，文本框中之前的内容会被清除掉。比如，你现在这个文本框 A 中输入了 "What" ，之后去编辑文本框 B，若再回来编辑文本框 A ，则其中的 "What" 会被立即清除。
1. Text Color ： 设置文本框中文本的颜色。
1. Font ： 设置文本的字体与字号。
1. Min Font Size ： 设置文本框可以显示的最小字体（不过我感觉没什么用）
1. Adjust To Fit ： 指定当文本框尺寸减小时，文本框中的文本是否也要缩小。选择它，可以使得全部文本都可见，即使文本很长。但是这个选项要跟 Min Font Size 配合使用，文本再缩小，也不会小于设定的 Min Font Size 。
接下来的部分用于设置键盘如何显示。
1. Captitalization ： 设置大写。下拉菜单中有四个选项：
    * None ： 不设置大写
    * Words ： 每个单词首字母大写，这里的单词指的是以空格分开的字符串
    * Sentances ： 每个句子的第一个字母大写，这里的句子是以句号加空格分开的字符串
    * All Characters ： 所以字母大写
1. Correction ： 检查拼写，默认是 YES 。
1. Keyboard ： 选择键盘类型，比如全数字、字母和数字等。
1. Appearance：
1. Return Key ： 选择返回键，可以选择 Search 、 Return 、 Done 等。
1. Auto-enable Return Key ： 如选择此项，则只有至少在文本框输入一个字符后键盘的返回键才有效。
1. Secure ： 当你的文本框用作密码输入框时，可以选择这个选项，此时，字符显示为星号。


1. Alignment Horizontal 水平对齐方式
1. Alignment Vertical 垂直对齐方式
1. 用于返回一个BOOL值　输入框是否 Selected(选中) Enabled(可用) Highlighted(高亮)

### 限制只能输入特定的字符

```objc
(BOOL)textField:(UITextField *)textField shouldChangeCharactersInRange:(NSRange)range replacementString:(NSString *)string {
    NSCharacterSet *cs;

    //invertedSet方法是取反字符,把所有的除了 NUMBERS 里的字符都找出来(包含去空格功能)
    cs = [[NSCharacterSet characterSetWithCharactersInString:NUMBERS] invertedSet];

    // componentsJoinedByString 用于根据一个字符串来将数组连接成一个新的字符串。
    NSString *filtered = [[string componentsSeparatedByCharactersInSet:cs]componentsJoinedByString:@""]; //按cs分离出数组,数组按@""分离出字符串

    BOOL canChange = [string isEqualToString:filtered];

    return canChange;
    //这样写了以后，输入非数字时不能输入
}
```

上面那个 `NUMBERS` 是一个宏，可以在文件顶部定义：

`#define NUMBERS @”0123456789n”` （这个代表可以输入数字和换行，请注意这个n，如果不写这个，Done 按键将不会触发，如果用在SearchBar 中，将会不触发 Search 事件，因为你自己限制不让输入n，好惨，我在项目中才发现的。）

所以，如果你要限制输入英文和数字的话，就可以把这个定义为：

```objc
#define kAlphaNum   @”ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789″。
```
当然，你还可以在以上方法return之前，做一提示的，比如提示用户只能输入数字之类的。如果你觉得有需要的话。

### 限制只能输入一定长度的字符


```objc
- (BOOL)textField:(UITextField *)textField shouldChangeCharactersInRange:(NSRange)range replacementString:(NSString *)string; 
{　
    //string就是此时输入的那个字符 textField就是此时正在输入的那个输入框 返回YES就是可以改变输入框的值 NO相反

    if ([string isEqualToString:@"n"])　
    //按回车可以改变 
    //这里这个字符写什么，就只能输入什么
    { 
        return YES; 
    } 

    NSString * toBeString = [textField.text stringByReplacingCharactersInRange:range withString:string]; //得到输入框的内容

    if (self.myTextField == textField)　
    //判断是否时我们想要限定的那个输入框
    { 
        if ([toBeString length] > 20) { 
        //如果输入框内容大于20则弹出警告
    textField.text = [toBeString substringToIndex:20]; 
    UIAlertView *alert = [[[UIAlertView alloc] initWithTitle:nil message:@"超过最大字数不能输入了" delegate:nil cancelButtonTitle:@"Ok" otherButtonTitles:nil, nil] autorelease]; 
    //iOS8.3以后用 UIAlertController [alert show]; 

    /*
    UIAlertController *alertView = [UIAlertController alertControllerWithTitle:@"提示"message:@"超过最大字数不能输入了"preferredStyle:UIAlertControllerStyleActionSheet];
        [selfpresentViewController:alertViewanimated:YEScompletion:nil];
    */

            return NO; 
        } 
    } 
    return YES; 
}
```
