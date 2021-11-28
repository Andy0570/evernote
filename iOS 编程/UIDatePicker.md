**UIDatePicker** 日期选择器是 iOS 中用于输入日期和时间的控件。 你可以使用日期选择器来允许用户输入时间点（日历日期，时间值或两者）或时间间隔（例如定时器）。 日期选择器会向与其相关联的目标对象（target）报告 用户交互行为。



## Attributes 属性

###  核心属性

| 属性           | 描述                                       |
| :----------- | ---------------------------------------- |
| `Mode`     | 日期选择器模式。 确定日期选择器是否应显示时间，日期，时间和日期或倒计时间隔。 在运行时使用 `datePickerMode` 属性访问该值。 |
| `Locale`   | 与日期选择器关联的区域设置。 该属性允许你设置指定区域来覆盖系统默认值。 您可以使用`locale` 属性以编程方式访问该属性。 |
| `Interval` | 分钟旋转器的间隔，如果在当前模式下显示。 默认值为1，最大值为30.您选择的值必须是除数为60（1,2,3,4,5,6,10,12,15,20,30）的除数。 在运行时使用 `minuteInterval` 属性访问该值。 |

### 时间属性

|       属性        | 描述                                       |
| :-------------: | ---------------------------------------- |
|    `Date`     | 日期选择器将显示的初始日期。 默认为当前日期，但您可以设置自定义值。 此属性等效于以编程方式设置 `date` 属性。 |
| `Constraints` | 日期选择器显示的可选日期的范围。 要使用动态范围，以编程方式配置 `minimumDate` 和`maximumDate` 属性。 当 **Mode** 属性设置为倒计时器时，日期选择器将忽略这些选项。 |
|    `Timer`    | 日期选择器在倒计时模式下使用的初始值。 该值以秒为单位，但以分钟为单位显示。   |

 

## Symbols

### 管理日期和日历

* `calendar`
* `date`
* `locale`
* `setDate:animated:`
* `timeZone`

### 配置日期选择器模式

* `datePickerMode`

  ```objectivec
  typedef enum UIDatePickerMode : NSInteger {
      UIDatePickerModeTime,
      UIDatePickerModeDate,
      UIDatePickerModeDateAndTime,
      UIDatePickerModeCountDownTimer
  } UIDatePickerMode;
  ```

### 配置时间属性

* `maximumDate`

  日期选择器可以显示的最大日期。

* `minimumDate`

  日期选择器可以显示的最小日期。

* `minuteInterval`

  日期选择器应显示分钟的间隔。

* `countDownDuration`

  当 **mode** 属性设置为 `UIDatePickerModeCountDownTimer` 时，日期选择器显示的值。

### 常量

* `UIDatePickerMode`



## 使用

### 示例一：

```objectivec
- (void)viewDidLoad {
    [super viewDidLoad];
    
    CGRect frame = CGRectMake(27, 100, 320, 216);
    // 创建 UIDatePicker 对象
    UIDatePicker *datePicker = [[UIDatePicker alloc] initWithFrame:frame];
    // 设置背景颜色
    datePicker.backgroundColor = [UIColor flatWhiteColor];
    // 设置日期选择器模式:日期模式
    datePicker.datePickerMode = UIDatePickerModeDate;
    // 设置可供选择的最小时间：昨天
    NSTimeInterval time = 24 * 60 * 60; // 24H 的时间戳值
    datePicker.minimumDate = [[NSDate alloc] initWithTimeIntervalSinceNow:- time];
    // 设置可供选择的最大时间：明天
    datePicker.maximumDate = [[NSDate alloc] initWithTimeIntervalSinceNow:time];
    // 添加 Target-Action
    [datePicker addTarget:self
                   action:@selector(datePickerValueChanged:)
         forControlEvents:UIControlEventValueChanged];
    // 将 UIDatePicker 对象添加到当前视图
    [self.view addSubview:datePicker];
}

- (void)datePickerValueChanged:(id)sender {
    // 直接打印 Data
//    UIDatePicker *datePicker = (UIDatePicker *)sender;
//    NSDate *date = datePicker.date;
//    NSLog(@"%@",date); // Sun Jul 30 15:28:17 2017
    
    // 格式化日期后再打印
    UIDatePicker *datePicker = (UIDatePicker *)sender;
    NSDate *date = datePicker.date;
    NSDateFormatter *dateFormatter = [[NSDateFormatter alloc] init];
    [dateFormatter setDateFormat:@"yyyy年MM月dd日"];
    NSString *string = [dateFormatter stringFromDate:date];
    NSLog(@"%@",string); // 2017年07月29日
}
```

效果：

![](http://upload-images.jianshu.io/upload_images/2648731-ba21bd805118115c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)



### 示例二：点击 UITextField 弹出 UIDatePicker 日期选择器

```objectivec
#import "ViewController.h"
#import "Chameleon.h"

@interface ViewController () <UITextFieldDelegate>

@property (nonatomic, strong) UITextField *textField;
@property (nonatomic ,strong) UITextField *otherTextField;
@property (nonatomic, strong) UIDatePicker *datePicker;

@end

@implementation ViewController

#pragma mark - Lifecycle

- (void)viewDidLoad {
    [super viewDidLoad];
    
    [self.view addSubview:self.textField];
    [self.view addSubview:self.otherTextField];
}

#pragma mark - Custom Accessors

- (UITextField *)textField {
    if (!_textField) {
        _textField = [[UITextField alloc] initWithFrame:CGRectMake(30, 100, 320, 44)];
        _textField.backgroundColor = [UIColor flatLimeColor];
        _textField.returnKeyType = UIReturnKeyDone;
        _textField.tag = 1001;
        _textField.delegate = self;
    }
    return _textField;
}

- (UITextField *)otherTextField {
    if (!_otherTextField) {
        _otherTextField = [[UITextField alloc] initWithFrame:CGRectMake(30, 160, 320, 44)];
        _otherTextField.backgroundColor = [UIColor flatBlueColor];
        _otherTextField.returnKeyType = UIReturnKeyDone;
        _otherTextField.tag = 1002;
        _otherTextField.delegate = self;
    }
    return _otherTextField;
}

- (UIDatePicker *)datePicker {
    if (!_datePicker) {
        // 创建 UIDatePicker 对象
        _datePicker = [[UIDatePicker alloc] init];
        // 设置背景颜色
        _datePicker.backgroundColor = [UIColor flatWhiteColor];
        // 设置日期选择器模式:日期模式
        _datePicker.datePickerMode = UIDatePickerModeDate;
        // 设置可供选择的最小时间：昨天
        NSTimeInterval time = 24 * 60 * 60; // 24H 的时间戳值
        _datePicker.minimumDate = [[NSDate alloc] initWithTimeIntervalSinceNow:- time];
        // 设置可供选择的最大时间：明天
        _datePicker.maximumDate = [[NSDate alloc] initWithTimeIntervalSinceNow:time];
        // 添加 Target-Action
        [_datePicker addTarget:self
                        action:@selector(datePickerValueChanged:)
              forControlEvents:UIControlEventValueChanged];
    }
    return _datePicker;
}

#pragma mark - IBActions

- (void)datePickerValueChanged:(id)sender {
    UIDatePicker *datePicker = (UIDatePicker *)sender;
    NSDate *date = datePicker.date;
    NSDateFormatter *dateFormatter = [[NSDateFormatter alloc] init];
    [dateFormatter setDateFormat:@"yyyy年MM月dd日"];
    NSString *string = [dateFormatter stringFromDate:date];
    // 将日期赋值给 textField
    self.textField.text = string;
}

#pragma mark - UITextFieldDelegate

- (BOOL)textFieldShouldBeginEditing:(UITextField *)textField {
    // 如果需要显示键盘，则隐藏 datePicker
    if (textField.tag != 1001) {
        if (self.datePicker.superview) {
            [self.datePicker removeFromSuperview];
        }
        return YES;
    }
    
    // 如果需要弹出日期选择器，则隐藏所有键盘
    [self.view endEditing:YES];
   
    // 以动画的方式显示日期选择器
    self.datePicker.frame = CGRectMake(0, [UIScreen mainScreen].bounds.size.height, [UIScreen mainScreen].bounds.size.width, 216);
    [self.view addSubview:self.datePicker];
    
    [UIView animateWithDuration:0.3f animations:^{
        self.datePicker.frame = CGRectMake(0, [UIScreen mainScreen].bounds.size.height - 216, [UIScreen mainScreen].bounds.size.width, 216);
    } completion:^(BOOL finished) {
        
    }];
    return NO;
}

- (BOOL)textFieldShouldReturn:(UITextField *)textField {
    return YES;
}

@end
```

效果：

![](http://upload-images.jianshu.io/upload_images/2648731-25ef5b745192e9b3.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/310)


### 示例2-2
将`textFileDate.inputView` 设置成 **UIDatePicker** 可能更好：

```objectivec
// 1  声明datePicker设置弹出日期键盘的格式
datePicker = [[UIDatePicker alloc] init];

//datePicker.datePickerMode = UIDatePickerModeDateAndTime;
//datePicker.minuteInterval = 30;

[datePicker setDatePickerMode:UIDatePickerModeDate];

[datePicker addTarget:self
               action:@selector(chooseDate:)
     forControlEvents:UIControlEventValueChanged];

// 选择日期
-(void)chooseDate:(UIDatePicker *)sender {

NSDate *selectedDate = sender.date;
NSDateFormatter *formatter = [[NSDateFormatter alloc] init];
formatter.dateFormat = @"yyyy-MM-dd";
NSString *dateString = [formatter stringFromDate:selectedDate];
textFileDate.text = dateString;
}

// 设置输入的界面是时间选择的datepicker
textFileDate.inputView = datePicker;
```

### 示例三：

项目需求需要做一个日期选择器，只显示年月，不需要显示日。

网上找到这篇文章，[iOS-日期选择控件(可单独选年/年月/年月日)](http://www.jianshu.com/p/840510e92f71) ，看源码实现非常麻烦。

看到 stackoverflow 上的提问 [UIDatePicker select Month and Year](https://stackoverflow.com/questions/3348247/uidatepicker-select-month-and-year) 

推荐还是自己写一个 `UIPickerView` 实现可能比较方便。















## 附录1 UIDatePicker源码

```objectivec
//
//  UIDatePicker.h
//  UIKit
//
//  Copyright (c) 2006-2015 Apple Inc. All rights reserved.
//

#import <Foundation/Foundation.h>
#import <UIKit/UIControl.h>
#import <UIKit/UIKitDefines.h>

NS_ASSUME_NONNULL_BEGIN

// 日期选择器模式
typedef NS_ENUM(NSInteger, UIDatePickerMode) {
    UIDatePickerModeTime,           // 根据区域设置显示小时，分钟和可选的AM/PM（例如 6 | 53 | PM）
    UIDatePickerModeDate,           // 根据区域设置显示年月日（例如，11 | 15 | 2007）
    UIDatePickerModeDateAndTime,    // 根据区域设置显示日期，小时，分钟和可选的AM/PM（例如 Wed Nov 15 | 6 | 53 | PM）
    UIDatePickerModeCountDownTimer, // 显示小时和分钟（例如 1 | 53）
} __TVOS_PROHIBITED;

// ****************************📌 UIDatePicker 头文件*****************************
NS_CLASS_AVAILABLE_IOS(2_0) __TVOS_PROHIBITED @interface UIDatePicker : UIControl <NSCoding>

// 日期选择器模式，默认为 UIDatePickerModeDateAndTime
@property (nonatomic) UIDatePickerMode datePickerMode; 

// 区域，默认为 [NSLocale currentLocale]. 设置为 nil 则返回默认值
@property (nullable, nonatomic, strong) NSLocale   *locale;

// 日历，默认 [NSCalendar currentCalendar]. 设置为 nil 则返回默认值
@property (null_resettable, nonatomic, copy)   NSCalendar *calendar;

// 时区，默认为 nil. use current time zone or time zone from calendar
@property (nullable, nonatomic, strong) NSTimeZone *timeZone; 

// 日期，当选择器创建时默认为当前日期。当选择 UIDatePickerModeCountDownTimer 模式时会被忽略，且选择器以 0:00 为起点
@property (nonatomic, strong) NSDate *date;        

// 指定最小/最大日期范围。 默认值为 nil。 当 min> max 时，这些值将被忽略。 
// 选择 UIDatePickerModeCountDownTimer 模式时忽略该值。
@property (nullable, nonatomic, strong) NSDate *minimumDate; 
@property (nullable, nonatomic, strong) NSDate *maximumDate; 

// 倒计时持续时间
// 当日期选择器的 mode 属性设置为 UIDatePickerModeCountDownTimer 时，使用此属性获取并设置当前选定的值。 此属性的类型为 NSTimeInterval，因此以秒为单位，尽管日期选择器仅显示小时和分钟。 如果日期选择器的模式不是 UIDatePickerModeCountDownTimer，则此值为 undefined; 参考date属性。 默认值为0.0，最大值为23:59（86,399秒）。
@property (nonatomic) NSTimeInterval countDownDuration; 

// 显示分钟转盘的时间间隔。 间隔必须均匀分为60.默认值为1. min为1，max为30
@property (nonatomic) NSInteger      minuteInterval;

// 如果动画为YES，则以动画方式转动时间转盘来显示新的日期
- (void)setDate:(NSDate *)date animated:(BOOL)animated; 

@end

NS_ASSUME_NONNULL_END
```
