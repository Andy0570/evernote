# UIPickerView

**UIPickerView** 是使用旋转轮或类似老虎机来显示一组或多组值的视图。

**时钟** 应用中设置时间就是通过 **UIPickerView** 来实现的。

![系统时钟](http://upload-images.jianshu.io/upload_images/2648731-ae1ae9d9afbd6dbb.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/320)

![彩云天气-设置语言](http://upload-images.jianshu.io/upload_images/2648731-0da6441ff80d6273.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/320)




> **UIPickerView.h** 的源码见附录。



### 使用

### 单列选择器

```objective-c
#import "ViewController.h"

@interface ViewController () <UIPickerViewDataSource, UIPickerViewDelegate>

@property (nonatomic, strong) UIPickerView *pickerView; // 选择器视图
@property (nonatomic, copy) NSArray *dataSourceArray;   // 数据源数组

@end

@implementation ViewController

#pragma mark - Lifecycle

- (void)viewDidLoad {
    [super viewDidLoad];
    // 设置选择器数据源
    self.dataSourceArray = @[@"iMac",@"iPad",@"iPhone",@"iWatch",@"iPod"];
    // 添加选择器视图
    [self.view addSubview:self.pickerView];
}

#pragma mark - Custom Accessors

- (UIPickerView *)pickerView {
    if (!_pickerView) {
        CGRect frame = CGRectMake(50, 100, 275, 300);
        _pickerView = [[UIPickerView alloc] initWithFrame:frame];
        _pickerView.backgroundColor = [UIColor flatWhiteColor];
        _pickerView.dataSource = self;
        _pickerView.delegate = self;
    }
    return _pickerView;
}

#pragma mark - UIPickerViewDataSource

// 返回需要展示的列（columns）的数目
- (NSInteger)numberOfComponentsInPickerView:(UIPickerView *)pickerView {
    return 1;
}

// 返回每一列的行（rows）数
- (NSInteger)pickerView:(UIPickerView *)pickerView numberOfRowsInComponent:(NSInteger)component {
    return self.dataSourceArray.count;
}

#pragma mark - UIPickerViewDelegate

// 返回每一行的标题
- (NSString *)pickerView:(UIPickerView *)pickerView titleForRow:(NSInteger)row forComponent:(NSInteger)component {
    return self.dataSourceArray[row];
}

// 某一行被选择时调用
- (void)pickerView:(UIPickerView *)pickerView didSelectRow:(NSInteger)row inComponent:(NSInteger)component {
    NSString *item = self.dataSourceArray[row];
    NSLog(@"第 %lu 项的 %@ 被选择了！",row,item);
}
```

 显示效果

![](http://upload-images.jianshu.io/upload_images/2648731-3b1911598de5849c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/320)

#### 初始化选中中间项

```objective-c
// 初始化选中中间一项
[self.pickerView selectRow:2 inComponent:0 animated:NO];
```

这个方法可以让选择器初始化时选中指定项，这样它就会居中显示

效果如下：

![](http://upload-images.jianshu.io/upload_images/2648731-b9d287ff1353ca05.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)







### 多列选择器

```objective-c
#import "ViewController.h"

@interface ViewController () <UIPickerViewDataSource, UIPickerViewDelegate>

// 选择器视图
@property (nonatomic, strong) UIPickerView *pickerView;
@property (nonatomic, copy) NSArray *column1Array;
@property (nonatomic, copy) NSArray *column2Array;

@end

@implementation ViewController

#pragma mark - Lifecycle

- (void)viewDidLoad {
    [super viewDidLoad];
    
    // 设置选择器数据源
    self.column1Array = @[@"iMac",@"iPad",@"iPhone",@"iWatch",@"iPod"];
    self.column2Array = @[@"MacBook",@"MacBook Air",@"MacBook Pro",@"iMac",@"iMac Pro",@"Mac Pro",@"Mac mini"];
    
    // 添加选择器视图
    [self.view addSubview:self.pickerView];
}

#pragma mark - Custom Accessors

- (UIPickerView *)pickerView {
    if (!_pickerView) {
        CGRect frame = CGRectMake(50, 100, 275, 300);
        _pickerView = [[UIPickerView alloc] initWithFrame:frame];
        _pickerView.backgroundColor = [UIColor flatWhiteColor];
        _pickerView.dataSource = self;
        _pickerView.delegate = self;
    }
    return _pickerView;
}

#pragma mark - UIPickerViewDataSource

// 返回需要展示的列（columns）的数目
- (NSInteger)numberOfComponentsInPickerView:(UIPickerView *)pickerView {
    // 设置返回两列
    return 2;
}

// 返回每一列的行（rows）数
- (NSInteger)pickerView:(UIPickerView *)pickerView numberOfRowsInComponent:(NSInteger)component {
    switch (component) {
        case 0:
            // 第一列返回的行数
            return self.column1Array.count;
            break;
        case 1:
            // 第二列返回的行数
            return self.column2Array.count;
            break;
        default:
            return 0;
            break;
    }
}

#pragma mark - UIPickerViewDelegate

// 设置组件的宽度
- (CGFloat)pickerView:(UIPickerView *)pickerView widthForComponent:(NSInteger)component {
    if (component == 0) {
        return 90;
    }else {
        return 210;
    }
}

// 设置每行的高度
//- (CGFloat)pickerView:(UIPickerView *)pickerView rowHeightForComponent:(NSInteger)component {
//    
//}

// 返回每一行的标题
- (NSString *)pickerView:(UIPickerView *)pickerView titleForRow:(NSInteger)row forComponent:(NSInteger)component {
    switch (component) {
        case 0:
            // 第一列的标题
            return self.column1Array[row];
            break;
        case 1:
            // 第二列的标题
            return self.column2Array[row];
        default:
            return nil;
            break;
    }
}

// 某一行被选择时调用
- (void)pickerView:(UIPickerView *)pickerView didSelectRow:(NSInteger)row inComponent:(NSInteger)component {
    NSString *column1;
    NSString *column2;
    // 只会进入一个 case 分支, 不会两个分支同时执行
    switch (component) {
        case 0: {
            column1 = self.column1Array[row];
            break;
        }
        case 1: {
            column2 = self.column2Array[row];
            break;
        }
        default:
            break;
    }
    NSLog(@"%@ - %@",column1,column2);
}

@end

```

显示效果

![](http://upload-images.jianshu.io/upload_images/2648731-81f0573b8a46bb52.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/320)

### 相互依赖的多列选择器

```objective-c
#import "ViewController.h"

@interface ViewController () <UIPickerViewDataSource, UIPickerViewDelegate>

// 选择器视图
@property (nonatomic, strong) UIPickerView *pickerView;

@property (nonatomic, copy) NSArray *column0Array; // 第一列
@property (nonatomic, copy) NSArray *column01Array; //与第一列相关联的第二列

@property (nonatomic, copy) NSArray *column1Array;
@property (nonatomic, copy) NSArray *column2Array;
@property (nonatomic, copy) NSArray *column3Array;
@property (nonatomic, copy) NSArray *column4Array;
@property (nonatomic, copy) NSArray *column5Array;

@end

@implementation ViewController

#pragma mark - Lifecycle

- (void)viewDidLoad {
    [super viewDidLoad];
    
    // 设置选择器数据源
    self.column0Array = @[@"Mac",@"iPad",@"iPhone",@"Watch",@"TV"];
    
    self.column1Array = @[@"MacBook",@"MacBook Air",@"MacBook Pro",@"iMac",@"iMac Pro",@"Mac Pro",@"Mac mini"];
    self.column2Array = @[@"iPad Pro",@"iPad",@"iPad mini 4"];
    self.column3Array = @[@"iPhone 7",@"iPhone 6s",@"iPhone SE"];
    self.column4Array = @[@"Apple Watch Serries 2",@"Apple Watch Nike+",@"Apple Watch Herms",@"Apple Watch Edition",@"Apple Watch Serries 1"];
    self.column5Array = @[@"Apple TV"];
    
    // 初始化第二列
    self.column01Array = self.column1Array;
    
    // 添加选择器视图
    [self.view addSubview:self.pickerView];
}

#pragma mark - Custom Accessors

- (UIPickerView *)pickerView {
    if (!_pickerView) {
        CGRect frame = CGRectMake(50, 100, 300, 300);
        _pickerView = [[UIPickerView alloc] initWithFrame:frame];
        _pickerView.backgroundColor = [UIColor flatWhiteColor];
        _pickerView.dataSource = self;
        _pickerView.delegate = self;
    }
    return _pickerView;
}

#pragma mark - UIPickerViewDataSource

// 返回需要展示的列（columns）的数目
- (NSInteger)numberOfComponentsInPickerView:(UIPickerView *)pickerView {
    // 设置返回两列
    return 2;
}

// 返回每一列的行（rows）数
- (NSInteger)pickerView:(UIPickerView *)pickerView numberOfRowsInComponent:(NSInteger)component {
    switch (component) {
        case 0:
            // 第一列返回的行数
            return self.column0Array.count;
            break;
        case 1:
            // 第二列返回的行数
            return self.column01Array.count;
            break;
        default:
            return 0;
            break;
    }
}

#pragma mark - UIPickerViewDelegate

// 设置组件的宽度
- (CGFloat)pickerView:(UIPickerView *)pickerView widthForComponent:(NSInteger)component {
    if (component == 0) {
        return 90;
    }else {
        return 210;
    }
}

// 返回每一行的标题
- (NSString *)pickerView:(UIPickerView *)pickerView titleForRow:(NSInteger)row forComponent:(NSInteger)component {
    switch (component) {
        case 0:
            // 第一列的标题
            return self.column0Array[row];
            break;
        case 1:
            // 第二列的标题
            return self.column01Array[row];
        default:
            return nil;
            break;
    }
}

// 某一行被选择时调用
- (void)pickerView:(UIPickerView *)pickerView didSelectRow:(NSInteger)row inComponent:(NSInteger)component {
    if (component == 0) {
        // 根据选择的左边列，修改右边列数组
        if (row == 0) {
            self.column01Array = self.column1Array;
        }else if (row == 1) {
            self.column01Array = self.column2Array;
        }else if (row == 2) {
            self.column01Array = self.column3Array;
        }else if (row == 3) {
            self.column01Array = self.column4Array;
        }else {
            self.column01Array = self.column5Array;
        }
        // 刷新右边列数据
        [pickerView reloadComponent:1];
    }
}

@end

```

显示效果

![](http://upload-images.jianshu.io/upload_images/2648731-4086b42f25333616.gif?imageMogr2/auto-orient/strip)





### 自定义视图

```objective-c
// 返回自定义视图
- (UIView *)pickerView:(UIPickerView *)pickerView viewForRow:(NSInteger)row forComponent:(NSInteger)component reusingView:(nullable UIView *)view {

    if (view == nil) {
        view = [[UIView alloc] init];
    }
    view.backgroundColor = [UIColor randomFlatColor];
    return view;
}
```

该方法可以返回任意 **UIView** 或其子类对象，比如 **UILabel**。



显示效果

![](http://upload-images.jianshu.io/upload_images/2648731-006f5f2ce503291f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/320)



## 附录1、UIPickerView 源码

```objective-c
//
//  UIPickerView.h
//  UIKit
//
//  Copyright (c) 2006-2015 Apple Inc. All rights reserved.
//

#import <Foundation/Foundation.h>
#import <CoreGraphics/CoreGraphics.h>
#import <UIKit/UIView.h>
#import <UIKit/UIKitDefines.h>

NS_ASSUME_NONNULL_BEGIN

@protocol UIPickerViewDataSource, UIPickerViewDelegate;

// **********************📌 UIPickerView 头文件******************************

NS_CLASS_AVAILABLE_IOS(2_0) __TVOS_PROHIBITED @interface UIPickerView : UIView <NSCoding>

@property(nullable,nonatomic,weak) id<UIPickerViewDataSource> dataSource;                // 数据源协议，默认为 nil. 弱引用
@property(nullable,nonatomic,weak) id<UIPickerViewDelegate>   delegate;                  // 委托协议，默认为 nil. 弱引用

/** 
 * 该属性控制是否显示UIPickerView中的选中标记（以高亮背景作为选中标记）
 * 
 * 在iOS 7及更高版本上，你无法自定义 UIPickerView 选择指示器。 
 * 选择指示器将始终显示，因此将此属性设置为NO无效。
 **/
@property(nonatomic)        BOOL                       showsSelectionIndicator;   // 默认为  NO

// 从数据源和委托中获取和缓存的信息
@property(nonatomic,readonly) NSInteger numberOfComponents; // 组件数目
- (NSInteger)numberOfRowsInComponent:(NSInteger)component;  // 指定组件中的 Item 项数目
- (CGSize)rowSizeForComponent:(NSInteger)component;         // 指定组件中 Item 项的尺寸

// 返回通过实现委托协议 pickerView:viewForRow:forComponent:reusingView: 得到的视图
// 如果 row/组件不可见或者没有实现 pickerView:viewForRow:forComponent:reusingView: 委托协议就返回 nil
- (nullable UIView *)viewForRow:(NSInteger)row forComponent:(NSInteger)component;

// 重新加载整个视图或单个组件
- (void)reloadAllComponents;
- (void)reloadComponent:(NSInteger)component;

// 选择项。 在这种情况下，意味着在中间显示适当的行
- (void)selectRow:(NSInteger)row inComponent:(NSInteger)component animated:(BOOL)animated;  // 将指定的行滚动到中心位置。

- (NSInteger)selectedRowInComponent:(NSInteger)component;                                   // 返回所选行。 如果没有选择就返回-1

@end


// **********************📌 UIPickerView 数据源协议******************************
__TVOS_PROHIBITED
@protocol UIPickerViewDataSource<NSObject>
@required // 必须实现

// 返回显示的“列”数。
- (NSInteger)numberOfComponentsInPickerView:(UIPickerView *)pickerView;

// 返回每个组件中的行数
- (NSInteger)pickerView:(UIPickerView *)pickerView numberOfRowsInComponent:(NSInteger)component;
@end

// **********************📌 UIPickerView 委托协议******************************
__TVOS_PROHIBITED
@protocol UIPickerViewDelegate<NSObject>
@optional

// 返回每个组件的列的宽度和行的高度。
- (CGFloat)pickerView:(UIPickerView *)pickerView widthForComponent:(NSInteger)component __TVOS_PROHIBITED;

// 关于高度值
- (CGFloat)pickerView:(UIPickerView *)pickerView rowHeightForComponent:(NSInteger)component __TVOS_PROHIBITED;

// 这些方法返回一个简单的 NSString 字符串，NSAttributedString 字符串或一个视图（如UILabel）来显示组件的行。 
// 对于视图版本，我们缓存任何隐藏的，未被使用的视图，并将它们传回以供重用。
// 如果您返回一个不同的对象，旧的将被释放。 视图将以行的 rect 为中心
// The methods in this group are marked @optional. However, to use a picker view, you must implement either the pickerView:titleForRow:forComponent: or the pickerView:viewForRow:forComponent:reusingView: method to provide the content of component rows. 下面两个协议两个必须实现其中一个以显示组件内容
- (nullable NSString *)pickerView:(UIPickerView *)pickerView titleForRow:(NSInteger)row forComponent:(NSInteger)component __TVOS_PROHIBITED;
- (nullable NSAttributedString *)pickerView:(UIPickerView *)pickerView attributedTitleForRow:(NSInteger)row forComponent:(NSInteger)component NS_AVAILABLE_IOS(6_0) __TVOS_PROHIBITED; // 如果两种方法都实现，attributed title 优先被选择
- (UIView *)pickerView:(UIPickerView *)pickerView viewForRow:(NSInteger)row forComponent:(NSInteger)component reusingView:(nullable UIView *)view __TVOS_PROHIBITED;

// 当用户选择组件中的某一行时，将会被调用。
- (void)pickerView:(UIPickerView *)pickerView didSelectRow:(NSInteger)row inComponent:(NSInteger)component __TVOS_PROHIBITED;

@end

NS_ASSUME_NONNULL_END
```





### 参考

* [UIKit－UIPickerView详解](http://www.jianshu.com/p/8bc79a704267)
