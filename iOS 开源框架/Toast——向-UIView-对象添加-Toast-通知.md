* GitHub: [Toast](https://github.com/scalessec/Toast)
* Star: 3k

## Toast for iOS

[Toast](https://github.com/scalessec/Toast) 是一个基于 Objective-C 语言的范畴（category）框架，它向 UIView 对象添加 Toast 通知。它简单、轻便且易于使用。大多数 Toast 通知都可以通过一行代码触发。

**如果你在使用 Swift? 这个框架也有一个原生的 Swift 版本支持: [Toast-Swift](https://github.com/scalessec/Toast-Swift "Toast-Swift")**

## 截屏

![](https://upload-images.jianshu.io/upload_images/2648731-4e2da7be591ba2ba.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 简单示例

```objectivec
// 基本用法
[self.view makeToast:@"This is a piece of toast."];

// 指定位置和持续时间
// duration：显示 Toast 的持续时间，默认显示 3.0 秒后自动消失。
// position：显示 Toast 的位置，
// 可选位置参数：CSToastPositionTop、CSToastPositionCenter、CSToastPositionBottom
[self.view makeToast:@"This is a piece of toast with a specific duration and position." 
            duration:3.0
            position:CSToastPositionTop];

// 带所有可自定义配置选项的 Toast
[self.view makeToast:@"This is a piece of toast with a title & image"
            duration:3.0
            position:[NSValue valueWithCGPoint:CGPointMake(110, 110)]
               title:@"Toast Title"
               image:[UIImage imageNamed:@"toast.png"]
               style:nil
          completion:^(BOOL didTap) {
              if (didTap) {
                  NSLog(@"completion from tap");
              } else {
                  NSLog(@"completion without tap");
              }
          }];
                
// 显示加载活动指示器的 Toast，这个方法类似于 MBProgressHUD
[self.view makeToastActivity:CSToastPositionCenter];

// 显示自定义 UIView 的 Toast
[self.view showToast:myView];
```

注：Toast 框架中的 `makeToast:` 这个方法其实也可以用 [MBProgressHUD](https://github.com/jdg/MBProgressHUD) 中的方法实现：
```objectivec
// 封装成通用一个方法
- (void)showMBProgressHudWithString:(NSString *)string {
    MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.view animated:YES];
    
    // Set the text mode to show only text
    hud.mode = MBProgressHUDModeText;
    hud.label.text = NSLocalizedString(string, @"HUD message title");
    // 移动到底部中心
    hud.offset = CGPointMake(0.f, MBProgressMaxOffset);
    // 3秒后自动消失
    [hud hideAnimated:YES afterDelay:3.f];
}
```

另外，Toast 中显示 Loading 动画的方法 `makeToastActivity:` 你也可以等同于 [MBProgressHUD](https://github.com/jdg/MBProgressHUD) 中的这个方法：

```objectivec
[MBProgressHUD showHUDAddedTo:self.view animated:YES];
```

## 等等，还有更多!

```objectivec
// CSToastStyle 类是用来创建自定义样式的
CSToastStyle *style = [[CSToastStyle alloc] initWithDefaultStyle];

// this is just one of many style options
// 文本颜色
style.messageColor = [UIColor orangeColor];

// present the toast with the new style
[self.view makeToast:@"This is a piece of toast."
            duration:3.0
            position:CSToastPositionBottom
               style:style];

// or perhaps you want to use this style for all toasts going forward?
// just set the shared style and there's no need to provide the style again
// 设置一个共享的自定义样式，然后通用所有 Toast
[CSToastManager setSharedStyle:style];

// toggle "tap to dismiss" functionality
// 打开 “点击以关闭” 功能
[CSToastManager setTapToDismissEnabled:YES];

// toggle queueing behavior
// 按顺序显示 Toast
[CSToastManager setQueueEnabled:YES];

// immediately hides all toast views in self.view
// 立即隐藏所有正在显示的 Toast
[self.view hideAllToasts];
```

自定义样式示例，在页面顶部显示一个自定义的 Toast：

```objectivec
CSToastStyle *style = [[CSToastStyle alloc] initWithDefaultStyle];
style.backgroundColor = [UIColor colorWithHue:168/360.0f saturation:86/100.0f brightness:74/100.0f alpha:1.0];
style.titleColor = [UIColor blackColor];
style.messageColor = [UIColor blackColor];
style.cornerRadius = 15.0;
[self.view makeToast:result duration:5.0 position:CSToastPositionTop style:style];
```


## 安装使用说明

### [CocoaPods](http://cocoapods.org)

Install with CocoaPods by adding the following to your `Podfile`:
```ruby
pod 'Toast', '~> 4.0.0'
```

### [Carthage](https://github.com/Carthage/Carthage)

Install with Carthage by adding the following to your `Cartfile`:
```ogdl
github "scalessec/Toast" ~> 4.0.0
```
Run `carthage update` to build the framework and link against `Toast.framework`. Then, `#import <Toast/Toast.h>`.

### 手动集成

1. 将 `UIView+Toast.h` 和 `UIView+Toast.m` 添加到你的项目中。
2. 使用时直接导入文件 `#import "UIView+Toast.h"` 即可。
3. 等待奇迹（原文是“Grab yourself a cold 🍺”）。

## MIT License

    Copyright (c) 2011-2017 Charles Scalesse.
    
    Permission is hereby granted, free of charge, to any person obtaining a
    copy of this software and associated documentation files (the
    "Software"), to deal in the Software without restriction, including
    without limitation the rights to use, copy, modify, merge, publish,
    distribute, sublicense, and/or sell copies of the Software, and to
    permit persons to whom the Software is furnished to do so, subject to
    the following conditions:
    
    The above copyright notice and this permission notice shall be included
    in all copies or substantial portions of the Software.
    
    THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS
    OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
    MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
    IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY
    CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
    TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
    SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

