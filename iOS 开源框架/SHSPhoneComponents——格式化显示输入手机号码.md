一个可以格式化显示手机号码的组件

* GitHub地址：[SHSPhoneComponents](https://github.com/Serheo/SHSPhoneComponent)
* star: 300+ 

![](http://upload-images.jianshu.io/upload_images/2648731-9536a3cd63a31f25.gif?imageMogr2/auto-orient/strip)
=================

用于格式化显示电话号码的 `UITextField` 和 `NSFormatter` 子类。 允许不同国家（模式）的不同格式。实现出色的插入符定位效果。

Swift 版本点击此处 - https://github.com/Serheo/PhoneNumberFormatter
## 如何安装
使用以下任意方法：

- 使用嵌入式框架 `/SHSPhoneComponents/SHSPhoneComponent.xcodeproj (iOS 8+)`
- pod 'SHSPhoneComponent' 
- 复制 `/SHSPhoneComponents/Library` 文件夹到项目中。

并且在视图控制器中 `import "SHSPhoneLibrary.h"`。

## 使用示例
如果你需要完整的示例，请查看  'Example_iOS7+' 【Objective-C版】 或者 'Example_iOS8+embedded' 【Swift版】文件夹。

### 设置默认格式
```objectivec
[self.phoneField.formatter setDefaultOutputPattern:@"+# (###) ###-##-##"];
```

![](http://upload-images.jianshu.io/upload_images/2648731-b693939724f9dca9.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

所有输入字符串将以此方式进行解析。
例如: +7 (920) 123-45-67

> ⚠️ 注意，不仅仅是显示时格式化手机号码，`self.phoneField.text` 获取到的手机号码也是被格式化的。 



### 前缀格式
你可以为所有的输入设置前缀：

```objectivec
[self.phoneField.formatter setDefaultOutputPattern:@"(###) ###-##-##"];
self.phoneField.formatter.prefix = @"+7 ";  // 初始化默认带 “+7” 前缀，用户无法删除
```
![](http://upload-images.jianshu.io/upload_images/2648731-c9d5259ea29f306e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 多格式支持

```objectivec
// 默认格式
[self.phoneField.formatter setDefaultOutputPattern:@"##########" imagePath:nil];

// 7开头格式 
[self.phoneField.formatter addOutputPattern:@"+# (###) ###-##-##" forRegExp:@"^7[0-689]\\d*$" imagePath:@"flagRU"];

// 374开头格式 
[self.phoneField.formatter addOutputPattern:@"+### (##) ###-###" forRegExp:@"^374\\d*$" imagePath:@"flagAM"];
```

![](http://upload-images.jianshu.io/upload_images/2648731-f31e861deccc26fa.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 多格式支持并且带前缀

```objectivec
[self.phoneField.formatter setDefaultOutputPattern:@"### ### ###"];
self.phoneField.formatter.prefix = @"+7 ";
[self.phoneField.formatter addOutputPattern:@"(###) ###-##-##" forRegExp:@"^1\\d*$" imagePath:@"SHSPhoneImage.bundle/flag_ru"];
[self.phoneField.formatter addOutputPattern:@"(###) ###-###" forRegExp:@"^2\\d*$" imagePath:@"SHSPhoneImage.bundle/flag_ua"];
```

### 指定格式
如果你想格式化一些数字的具体方式只要做
```objectivec
[self.phoneField.formatter addOutputPattern:@"+# (###) ###-##-##" forRegExp:@"^7[0-689]\\d*$" imagePath:@"flagRU"];
[self.phoneField.formatter addOutputPattern:@"+### (##) ###-###" forRegExp:@"^374\\d*$" imagePath:@"flagAM"];
```

## 格式化功能
如果您只需要格式化功能，可以使用SHSPhoneNumberFormatter类。
For additional class info see http://serheo.github.io/SHSPhoneComponent/

```objectivec
// 格式化显示
SHSPhoneNumberFormatter *formatter = [[SHSPhoneNumberFormatter alloc] init];
[formatter setDefaultOutputPattern:@"### #### ####"];
```

## Issues and Solutions
if you are using any predictions/suggestion in the textfield, set hasPredictiveInput flag to YES.

## Requirements
ARC Enabled.
iOS 7+

## 许可证
SHSPhoneComponent is available under the MIT license. See the LICENSE file for more info.



设置格式化的方法都在 `SHSPhoneNumberFormatter+UserConfig.h` 分类中

```objective-c
-(void) resetDefaultFormat;
-(void) resetFormats;

-(void) setDefaultOutputPattern:(NSString *)pattern imagePath:(NSString *)imagePath;
-(void) setDefaultOutputPattern:(NSString *)pattern;

-(void) addOutputPattern:(NSString *)pattern forRegExp:(NSString *)regexp imagePath:(NSString *)imagePath;
-(void) addOutputPattern:(NSString *)pattern forRegExp:(NSString *)regexp;

-(void) setOutputPatternsFromArray:(NSArray *)patterns;
```
