![登录界面](http://upload-images.jianshu.io/upload_images/2648731-9108087e9438f177.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

参考简书文章：
代码方式：[iOS制作一个漂亮的登录界面](http://www.jianshu.com/p/16766c358d19)
Xib方式：[登录注册界面的设计过程](http://www.jianshu.com/p/dd80b3fe3b42)

## 1.代码方式创建
* 首先创建viewController
* 还需要创建父视图为UIView的backgroundView用于绘制底部图层以及画用户名和密码textField之间的直线
* 最外层的灰色背景可在storyboard中设置

### 首先在viewController.h文件中定义相关属性
```
@interface LoginViewController : UIViewController
//属性：账户
@property(nonatomic,strong) UITextField *account;
//属性：密码
@property(nonatomic,strong) UITextField *password;
//属性：登录按钮
@property(nonatomic,strong) UIButton *loginButton;
//属性：用户绑定
@property(nonatomic,strong) UIButton *bindUser;
@end
```

### .m文件中实现

```
- (void)viewDidLoad {
    [super viewDidLoad];
    
    //设置导航栏标题&颜色
    self.navigationItem.title = @"登录";
    [self.navigationController.navigationBar setTitleTextAttributes:@{NSFontAttributeName:[UIFont systemFontOfSize:16],NSForegroundColorAttributeName:[UIColor orangeColor]}];
    
    //用户名&密码的底部图层
    HQLLoginTextFieldBackground *_background = [[HQLLoginTextFieldBackground alloc] initWithFrame:CGRectMake(20, 150, self.view.frame.size.width-40, 100)];
    [_background setBackgroundColor:[UIColor whiteColor]];
    [[_background layer] setCornerRadius:10.0];
    [[_background layer] setMasksToBounds:YES];
    [self.view addSubview:_background];
    
    //文本输入框：用户名
    _account = [[UITextField alloc] initWithFrame:CGRectMake(30, 150, self.view.frame.size.width-50, 50)];
    _account.backgroundColor = [UIColor clearColor];
    _account.placeholder = [NSString stringWithFormat:@"手机号码"]; //占位符
    _account.layer.cornerRadius = 10.0;  //圆角属性
    _account.clearButtonMode = UITextFieldViewModeWhileEditing; //清空按钮
    _account.keyboardType = UIKeyboardTypeNumberPad;    //软键盘样式：数字
    _account.tintColor = [UIColor blackColor];  //光标颜色
    _account.delegate = self;
    [self.view addSubview:_account];
    
    
    //文本输入框：密码
    _password = [[UITextField alloc] initWithFrame:CGRectMake(30, 200, self.view.frame.size.width-50, 50)];
    _password.backgroundColor = [UIColor clearColor];
    _password.placeholder = [NSString stringWithFormat:@"密码"];
    _password.layer.cornerRadius = 10.0; //圆角属性
    _password.clearButtonMode = UITextFieldViewModeWhileEditing; //清空按钮
    _password.secureTextEntry = YES;    //隐藏密码
    _password.returnKeyType = UIReturnKeyDone;  //返回键类型
    _password.tintColor = [UIColor blackColor]; //光标颜色
    _password.delegate = self;
    [self.view addSubview:_password];
    
    //按钮：登录
    _loginButton = [UIButton buttonWithType:UIButtonTypeSystem];
    [_loginButton setFrame:CGRectMake(20, 270, self.view.frame.size.width-40, 50)];
    [_loginButton setTitle:@"登   录" forState:UIControlStateNormal];
    [_loginButton setTitleColor:[UIColor whiteColor] forState:UIControlStateNormal];
    [_loginButton setBackgroundColor:[UIColor orangeColor]];
    [self.view addSubview:_loginButton];
    
    //按钮:用户绑定
    _bindUser = [UIButton buttonWithType:UIButtonTypeSystem];
    [_bindUser setFrame:CGRectMake(self.view.frame.size.width-100, 335, 80, 50)];
    [_bindUser setTitle:@"用户绑定" forState:UIControlStateNormal];
    [_bindUser setTitleColor:[UIColor blackColor] forState:UIControlStateNormal];
    [_bindUser setBackgroundColor:[UIColor clearColor]];
    [self.view addSubview:_bindUser];
}
```

## 还需要在用户名和密码的TextField之间划线


    - (void)drawRect:(CGRect)rect {
    //在用户名和密码之间添加线
    CGContextRef context = UIGraphicsGetCurrentContext();   //获得当前画板
    CGContextSetLineWidth(context, 0.2);    //设置线宽
    CGContextBeginPath(context);    //标记
    CGContextMoveToPoint(context, 5, 50);   //起始点
    CGContextAddLineToPoint(context, self.frame.size.width-5, 50);  //添加线
    CGContextClosePath(context);    //路径结束标志
    [[UIColor grayColor] setStroke];    //设置画线颜色
    CGContextStrokePath(context);   //绘制路径
    }
