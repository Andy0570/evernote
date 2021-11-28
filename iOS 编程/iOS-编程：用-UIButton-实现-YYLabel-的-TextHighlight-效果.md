![](http://upload-images.jianshu.io/upload_images/2648731-daa6acb78c516848?imageMogr2/auto-orient/strip)

### 方案一：使用 [YYText](https://github.com/ibireme/YYText) 框架

用如下方式实现文本链接效果：

```objectivec
- (void)viewDidLoad {
    [super viewDidLoad];

    YYLabel *label = [[YYLabel alloc] init];
    // 设置文本
    NSMutableAttributedString *attrStr = [[NSMutableAttributedString alloc] initWithString:@"查看链接地址"];
    attrStr.font = [UIFont systemFontOfSize:16];
    attrStr.underlineStyle = NSUnderlineStyleSingle;
    attrStr.underlineColor = HexColor(@"007AFF");
    [attrStr setTextHighlightRange:attrStr.rangeOfAll
                             color:HexColor(@"007AFF")
                   backgroundColor:HexColor(@"CECED2")
                         tapAction:^(UIView * _Nonnull containerView, NSAttributedString * _Nonnull text, NSRange range, CGRect rect) {
                             // Tap Action...
                         }];
    label.attributedText = attrStr;
    label.textAlignment = NSTextAlignmentCenter;
    [self.view addSubview:label];
    
    // 使用 Masonry 框架进行自动布局
    [label mas_makeConstraints:^(MASConstraintMaker *make) {
        make.centerX.equalTo(self.view);
        make.centerY.equalTo(self.view);
    }];
}
```

需要的效果是这样的，有下划线、点击后会有灰色背景：

![](http://upload-images.jianshu.io/upload_images/2648731-4029b347fd93c3bc.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

实际上效果是，没有下划线、点击后也没有灰色背景：

![](http://upload-images.jianshu.io/upload_images/2648731-4d02e21999d2449e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



#### 失效原因：

一开始我以为是 **Masonry** 自动布局的原因：

如果用 `[[YYLabel alloc] initWithFrame:CGRectMake(100, 300, 300, 21)];`  写成固定约束就没有问题。

后来发现原因只是这行代码：

```objectivec
label.textAlignment = NSTextAlignmentCenter; // 设置了居中显示
```

只要删掉这行代码就可以了。



### 方案二：UIButton 原生实现

当然，你也可以使用原生的 **UIButton** 类也可以实现以上效果。

```objectivec
@property (nonatomic, strong) UIButton *serviceAgreementButton;

- (UIButton *)serviceAgreementButton {
    if (!_serviceAgreementButton) {
        _serviceAgreementButton = [UIButton buttonWithType:UIButtonTypeCustom];
        // 默认标题
        NSDictionary *attributes = @{
                                     NSFontAttributeName:[UIFont systemFontOfSize:16],
                          NSForegroundColorAttributeName:HexColor(@"#007AFF"),
                           NSUnderlineStyleAttributeName:@(NSUnderlineStyleSingle)
                                     };
        NSAttributedString *attrStr = [[NSAttributedString alloc] initWithString:@"APP许可及服务协议" attributes:attributes];
        [_serviceAgreementButton setAttributedTitle:attrStr forState:UIControlStateNormal];
        // 高亮背景
        [_serviceAgreementButton setBackgroundImage:[UIImage imageWithColor:COLOR_BACKGROUND]
                                           forState:UIControlStateNormal];
        [_serviceAgreementButton setBackgroundImage:[UIImage imageWithColor:HexColor(@"#CECED2")]
                                           forState:UIControlStateHighlighted];
        [_serviceAgreementButton addTarget:self
                                    action:@selector(buttonTapAction:)
                          forControlEvents:UIControlEventTouchUpInside];
    }
    return _serviceAgreementButton;
}

- (void)viewDidLoad {
    [super viewDidLoad];
  
    [self.view addSubview:self.serviceAgreementButton];
	[self.serviceAgreementButton mas_makeConstraints:^(MASConstraintMaker *make) {
   		make.centerX.equalTo(self.view);
    	make.centerY.equalTo(self.view);
}];
}
```

![](http://upload-images.jianshu.io/upload_images/2648731-1c071a477a175a13.gif?imageMogr2/auto-orient/strip)

🎉🎉🎉  I GET IT !
