* 通过使用范畴（category，范畴、分类），可以为任何已有的类添加方法。
* ⚠️ 建议使用带有前缀（bnr_）的方法:实现了使用范畴的方法时，如果在类中已经存在名称相同的另一个方法，这个使用了范畴的方法就会替换掉之前存在的方法。
* 应该使用范畴来给已存在的类增加新方法，而不要在范畴中替换已经存在的方法：这种情况下应该创建该类的子类。

### 示例一

为 **NSDate** 类添加一个创建日期的类方法：

![](http://upload-images.jianshu.io/upload_images/2648731-e4af62edf9f2b313.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* **NSDate+VowelCounting.h** 文件

  ```objectivec
  #import <Foundation/Foundation.h>

  @interface NSDate (VowelCounting)

  + (NSString *)bnr_createCustomDate;

  @end
  ```

* **NSDate+VowelCounting.m** 文件

  ```objectivec
  #import "NSDate+VowelCounting.h"

  @implementation NSDate (VowelCounting)

  + (NSString *)bnr_createCustomDate {
      NSDateFormatter *dateFormatter = [[NSDateFormatter alloc] init];
      [dateFormatter setDateFormat:@"yyyy-MM-dd"];
      return [dateFormatter stringFromDate:[NSDate date]];
  }

  @end
  ```

* 使用

  ```objectivec
  NSString *date = [NSDate bnr_createCustomDate];
  ```

### 示例二

为 `UIView` 增加一个分类，增加设置或者获取 `UIView` 视图位置的便捷语法。
这个分类应该经常可以在很多开源框架中看到。

### UIView+Extension.h

```objectivec
#import <UIKit/UIKit.h>

@interface UIView (Extension)

// 推荐：CGFloat x = CGRectGetMinX(frame);
@property (nonatomic, assign) CGFloat x;
// 推荐：CGFloat y = CGRectGetMinY(frame);
@property (nonatomic, assign) CGFloat y;

@property (nonatomic, assign) CGFloat maxX;
@property (nonatomic, assign) CGFloat maxY;

@property (nonatomic, assign) CGFloat centerX;
@property (nonatomic, assign) CGFloat centerY;

// 推荐：CGFloat width = CGRectGetWidth(frame);
@property (nonatomic, assign) CGFloat width;
// 推荐：CGFloat height = CGRectGetHeight(frame);
@property (nonatomic, assign) CGFloat height;

@property (nonatomic, assign) CGSize size;
@property (nonatomic, assign) CGPoint origin;
@property (nonatomic, assign) CGFloat bottom;

@end
```


### UIView+Extension.m
```objectivec
#import "UIView+Extension.h"

@implementation UIView (Extension)

#pragma mark - x
- (void)setX:(CGFloat)x {
    CGRect frame = self.frame;
    frame.origin.x = x;
    self.frame = frame;
}

- (CGFloat)x {
    return CGRectGetMinX(self.frame);
}

#pragma mark - y
- (void)setY:(CGFloat)y {
    CGRect frame = self.frame;
    frame.origin.y = y;
    self.frame = frame;
}

- (CGFloat)y {
    return CGRectGetMinY(self.frame);
}

#pragma mark - maxX
- (void)setMaxX:(CGFloat)maxX {
    self.x = maxX - self.width;
}

- (CGFloat)maxX {
    return CGRectGetMaxX(self.frame);
}

#pragma mark - maxY
- (void)setMaxY:(CGFloat)maxY {
    self.y = maxY - self.height;
}

- (CGFloat)maxY {
    return CGRectGetMaxY(self.frame);
}

#pragma mark - centerX
- (void)setCenterX:(CGFloat)centerX {
    CGPoint center = self.center;
    center.x = centerX;
    self.center = center;
}

- (CGFloat)centerX {
    return self.center.x;
}

#pragma mark - centerY
- (void)setCenterY:(CGFloat)centerY {
    CGPoint center = self.center;
    center.y = centerY;
    self.center = center;
}

- (CGFloat)centerY {
    return self.center.y;
}

#pragma mark - width
- (void)setWidth:(CGFloat)width {
    CGRect frame = self.frame;
    frame.size.width = width;
    self.frame = frame;
}

- (CGFloat)width {
    return CGRectGetWidth(self.frame);
}

#pragma mark - height
- (void)setHeight:(CGFloat)height {
    CGRect frame = self.frame;
    frame.size.height = height;
    self.frame = frame;
}

- (CGFloat)height {
    return CGRectGetHeight(self.frame);
}

#pragma mark - size
- (void)setSize:(CGSize)size {
    CGRect frame = self.frame;
    frame.size = size;
    self.frame = frame;
}

- (CGSize)size {
    return self.frame.size;
}

#pragma mark - orgin
- (void)setOrigin:(CGPoint)origin {
    CGRect frame = self.frame;
    frame.origin = origin;
    self.frame = frame;
}

- (CGPoint)origin {
    return self.frame.origin;
}

#pragma mark  - bottom
- (void)setBottom:(CGFloat)bottom {
    CGRect frame = self.frame;
    frame.origin.y = bottom - frame.size.height;
    self.frame = frame;
}

- (CGFloat)bottom {
    CGRect frame = self.frame;
    return CGRectGetMinY(frame) + CGRectGetHeight(frame);
}

@end
```
  
