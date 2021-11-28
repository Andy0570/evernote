# 界面传值

界面传值就是在两个视图控制器之间跳转时传递属性信息。

###  A  →  B

从前向后传值，比如 **ViewController** 拥有的 **Person** 信息需要在跳转到 **SecondViewController** 时被 **SecondViewController** 接收到并对其做一些处理，最简单的方法就是在 **SecondViewController** 的头文件中声明一个 **Person** 属性：

```objective-c
#import <UIKit/UIKit.h>
@class Person;

@interface SecondViewController : UIViewController

@property (strong,nonatomic) Person *person;

@end
```



在 **ViewController** 跳转之前完成赋值操作：

```objective-c
SecondViewController *secondVC = [[SecondViewController alloc] init];

secondVC.person = self.person;

[self presentViewController:secondVC animated:YES completion:nil];
```



### B → A

从后面的视图控制器回传给上一个视图控制器， 比如 **ViewController** 跳转到 **SecondViewController** 之后，把 **SecondViewController** 的**Person** 信息回传给 **ViewController**，这时需要用到 **Protocol** 协议方法。相当于 **SecondViewController** 把信息给 **Protocol**，让 **ViewController** 遵守  **Protocol**  协议的方式来接收信息。

* 第一步，新建 **Delegate** 协议

新建一个 **Protocol**, （快捷键⌘ + N） ，选择 **Objective-C File**, **File Name** 输入 ：PassValueDelegate，**File Type** 选择 **Protocol**。

```objective-c
#import <Foundation/Foundation.h>
@class Person;

@protocol PassValueDelegate <NSObject>

- (void)passValue:(Person *)value;

@end
```



* 第二步，为  **SecondViewController** 设置 **Delegate** 属性，才能让  **ViewController** 去遵守。

```objective-c
#import <UIKit/UIKit.h>
#import "PassValueDelegate.h"

@interface SecondViewController : UIViewController

@property (nonatomic,assign) NSObject<PassValueDelegate> *delegate;

@end
```

> 注：相对于 assign ，我们一般使用 weak 来修饰 delegate，会更加安全。


* 第三步，声明 **ViewController**  遵守该协议并实现协议中的方法：

```objective-c
#import <UIKit/UIKit.h>
#import "PassValueDelegate.h"

@interface ViewController : UIViewController <PassValueDelegate>

@end
```

  .m文件中实现协议方法

  ```objective-c
@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view, typically from a nib.
}
- (IBAction)clearBtn:(id)sender {

    SecondViewController *secondVC = [[SecondViewController alloc] initWithNibName:@"SecondViewController" bundle:[NSBundle mainBundle]];

  // 遵守协议
    secondVC.delegate = self;

    [self presentViewController:secondVC animated:YES completion:nil];
}

- (void)passValue:(Person *)value {
    // 实现方法，处理接收到的属性信息
}

@end
  ```



* 第四步, **SecondViewController** 返回时，通过协议方法传值。

```objective-c
- (IBAction)OK:(id)sender {
    Person *person = [Person new];
    person.name = self.nameTextField.text;
    person.sex = self.sexTextField.text;
    person.age = self.ageTextField.text;
    
    [self.delegate passValue:person];
    [self.navigationController popViewControllerAnimated:YES];
    
}
```



## 登录跳转

需求分析：在APP中有些功能是需要用户登录才能使用的，例如在主界面点击功能A按钮，先判断该用户是否已经登录过，如果已经登录过，则直接跳转到相应的页面（功能A界面）；如果还没有登录，则跳转到登录页面进行登录，登录完成后自动跳转到功能A界面，而不是返回到主界面，需要用户再次点击该按钮才能进入相应的功能界面。直接跳转则更有利于提高用户体验。流程图如下：

![流程图](http://upload-images.jianshu.io/upload_images/2648731-77f9a3e2ea67e8c4.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

先看Demo：

![image](http://upload-images.jianshu.io/upload_images/2648731-c5154706c53ea467.gif?imageMogr2/auto-orient/strip)



详细设计：实现该需求需要同时使用到界面传值的两种方法:

* **主页** ➡ **登录页**，需要传入一个标记值👣，以告诉登录页面，是哪一个功能模块（A还是B）需要登录才跳转过来执行登录的。
* **登录页** ➡ **主页**，需要把刚才传进来的标记值👣回传给主页，告诉主页，刚才是你点了某个功能模块（A或者B）需要登录，我现在登录完成了，你自己再跳转进去吧。



#### 实现步骤

1. 新建一个登录成功协议：**SuccessLoginDelegate**

   SuccessLoginDelegate.h:

   ```objective-c
   #import <Foundation/Foundation.h>

   // 各功能标志值
   typedef NS_ENUM(NSInteger,functionPageName ){
       functionPageNameA = 1,
       functionPageNameB = 2,
   };

   @protocol SuccessLoginDelegate <NSObject>

   -(void)returnToViewController:(functionPageName )pageName;

   @end
   ```

2. 为登录页设置 **delegate** 属性

   ```
   #import <UIKit/UIKit.h>
   #import "SuccessLoginDelegate.h"

   @interface LoginViewController : UIViewController

   // 需要主页传入的标记值
   @property (nonatomic,assign) NSInteger index;

   // delegate 属性
   @property (nonatomic, assign) NSObject<SuccessLoginDelegate> *delegate;

   @end
   ```

3. 去主页实现 **delegate** 协议

   * 以功能A按钮为例：

     ```objective-c
     // 按钮A
     - (IBAction)buttonA_Click:(id)sender {
         BOOL isLogin = self.loginState.loginFlag;
         if (isLogin) {
           	// 如果已经登录，直接跳转
             [self showFirstFunctionViewControllerWithAnimation:YES];
         }else {
          	// 否则跳转到登录界面
             [self showLoginViewControllerWithIndex:1];
         }
     }

     // 进入功能A
     - (void)showFirstFunctionViewControllerWithAnimation:(BOOL)animated {
         FirstFunctionViewController *firstViewController = [[FirstFunctionViewController alloc] init];
         firstViewController.hidesBottomBarWhenPushed = YES;
         [self.navigationController pushViewController:firstViewController animated:animated];
     }

     // 打开登录页面
     - (void)showLoginViewControllerWithIndex:(NSInteger)index {
         UIStoryboard *storyBoard = [UIStoryboard storyboardWithName:@"main" bundle:nil];
         LoginViewController *loginViewController = [storyBoard instantiateViewControllerWithIdentifier:@"loginPage"];
         loginViewController.hidesBottomBarWhenPushed = YES;
         loginViewController.index = index;
         loginViewController.delegate = self;
         [self.navigationController pushViewController:loginViewController animated:YES];
     }
     ```

   * 实现的协议方法

     ```objective-c
     #pragma mark - SuccessLoginDelegate
     // 登录完成后跳转回主页执行该方法
     -(void)returnToViewController:(functionPageName )pageName {
       switch (pageName) {
           case functionPageNameA:
               [self showFirstFunctionViewControllerWithAnimation:NO];
               break;
           case functionPageNameB:
               [self showSecondFunctionViewControllerWithAnimation:NO];
               break;
           default:
               break;
       }
     ```

   4. 登录页简单放了两个按钮控制登录与否

      ```objective-c
      #pragma mark - IBAction
      // 登录
      - (IBAction)loginButton_Click:(id)sender {
          
          self.loginState.loginFlag = YES;
          
          UIAlertController *alertController = [UIAlertController alertControllerWithTitle:@"登录成功" message:nil preferredStyle:UIAlertControllerStyleAlert];
          UIAlertAction *action = [UIAlertAction actionWithTitle:@"确定" style:UIAlertActionStyleDefault handler:^(UIAlertAction * _Nonnull action) {
            	// 登录完成后返回
              [self.navigationController popViewControllerAnimated:NO];
            	// 把之前传进来的标记值回传过去
              [self.delegate returnToViewController:_index];
          }];
          [alertController addAction:action];
          [self presentViewController:alertController animated:YES completion:nil ];
      }

      // 退出登录
      - (IBAction)signOutButton_Click:(id)sender {
          
          self.loginState.loginFlag = NO;
          
          UIAlertController *alertController = [UIAlertController alertControllerWithTitle:@"退出成功" message:nil preferredStyle:UIAlertControllerStyleAlert];
          UIAlertAction *action = [UIAlertAction actionWithTitle:@"确定" style:UIAlertActionStyleDefault handler:^(UIAlertAction * _Nonnull action) {
              [self.navigationController popViewControllerAnimated:NO];
          }];
          [alertController addAction:action];
          [self presentViewController:alertController animated:YES completion:nil ];
      }

      ```
