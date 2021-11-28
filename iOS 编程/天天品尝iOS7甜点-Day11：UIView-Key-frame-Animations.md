> 视图关键帧动画


### 参考

- [GitHub 源码：shinobicontrols/iOS7-day-by-day](https://github.com/ShinobiControls/iOS7-day-by-day)
- [天天品尝iOS7甜点 :: Day 11 :: UIView Key-frame Animations](http://blog.kingiol.com/2014/01/13/ios7-day-by-day-day11-uiview-key-frame-animations/)


### 2个方法

```objective-c
@interface UIView (UIViewKeyframeAnimations)

// 关键帧动画方法
+ (void)animateKeyframesWithDuration:(NSTimeInterval)duration delay:(NSTimeInterval)delay options:(UIViewKeyframeAnimationOptions)options animations:(void (^)(void))animations completion:(void (^ __nullable)(BOOL finished))completion NS_AVAILABLE_IOS(7_0);

// 内部块方法
+ (void)addKeyframeWithRelativeStartTime:(double)frameStartTime relativeDuration:(double)frameDuration animations:(void (^)(void))animations NS_AVAILABLE_IOS(7_0); // start time and duration are values between 0.0 and 1.0 specifying time and duration relative to the overall time of the keyframe animation

@end
```



### UIViewKeyframeAnimationOptions 部分选项参数

```objective-c
UIViewKeyframeAnimationOptionCalculationModeLinear     = 0 << 10, // 线性动画，默认
UIViewKeyframeAnimationOptionCalculationModeDiscrete   = 1 << 10, // 分离动画
UIViewKeyframeAnimationOptionCalculationModePaced      = 2 << 10, // 进度动画
UIViewKeyframeAnimationOptionCalculationModeCubic      = 3 << 10, // 立体动画
UIViewKeyframeAnimationOptionCalculationModeCubicPaced = 4 << 10  // 立体+进度动画
```



#### 不同选项的行为示意图

水平轴代表了动画的时长，而竖直轴代表动画的参数(可以是视图的透明度，宽度)。

![](http://upload-images.jianshu.io/upload_images/2648731-37db268084e27c83.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### Rainbow Changer - 彩虹变色器

```objective-c
// 彩虹变色器方法
- (IBAction)handleRainbow:(id)sender {
    [self enableToolbarItems:NO];
    
    // 执行动画的 block
    void (^animationBlock)() = ^{
        NSArray *rainbowColors = @[[UIColor orangeColor],
                                   [UIColor yellowColor],
                                   [UIColor greenColor],
                                   [UIColor blueColor],
                                   [UIColor purpleColor],
                                   [UIColor redColor]];
        
        NSUInteger colorCount = [rainbowColors count];
        for (NSUInteger i=0; i<colorCount; i++) {
            // ❇️ 内部块方法，指定每一帧动画的相对起始时间、相对持续时间
            [UIView addKeyframeWithRelativeStartTime:i/(CGFloat)colorCount
                                    relativeDuration:1/(CGFloat)colorCount
                                          animations:^{
                                              self.rainbowView.backgroundColor = rainbowColors[i];
                                          }];
        }
    };
    
    // ❇️ 关键帧动画方法
    [UIView animateKeyframesWithDuration:4.0
                                   delay:0.0
                                 options:UIViewKeyframeAnimationOptionCalculationModeLinear | UIViewAnimationCurveLinear //线性、曲线
                              animations:animationBlock
                              completion:^(BOOL finished) {
                                  [self enableToolbarItems:YES];
                              }];
}

#pragma mark - Private

- (void)enableToolbarItems:(BOOL)enabled {
    for (UIBarButtonItem *item in self.toolbar.items) {
        item.enabled = enabled;
    }
}
```

效果：

![](http://upload-images.jianshu.io/upload_images/2648731-ac63a9580d17f4d8.gif?imageMogr2/auto-orient/strip)



### Rotation Directions - 旋转方向

#### 1. 顺时针旋转

```objective-c
- (IBAction)handleRotateCW:(id)sender {
    [self enableToolbarItems:NO];
    [UIView animateKeyframesWithDuration:2.0
                                   delay:0.0
                                 options:UIViewKeyframeAnimationOptionCalculationModeLinear
                              animations:^{
          // 0 ~ 2π/3
          [UIView addKeyframeWithRelativeStartTime:0.0
                                  relativeDuration:1/3.0
                                        animations:^{
                                            self.rotatingImageView.transform = CGAffineTransformMakeRotation(2.0 * M_PI / 3.0);
                                        }];
          // 2π/3 ~ 4π/3
          [UIView addKeyframeWithRelativeStartTime:1/3.0
                                  relativeDuration:1/3.0
                                        animations:^{
                                            self.rotatingImageView.transform = CGAffineTransformMakeRotation(4.0 * M_PI / 3.0);
                                        }];
          // 4π/3 ~ 0
          [UIView addKeyframeWithRelativeStartTime:2/3.0
                                  relativeDuration:1/3.0
                                        animations:^{
                                            self.rotatingImageView.transform = CGAffineTransformMakeRotation(0);
                                        }];
                              }
                              completion:^(BOOL finished) {
                                  [self enableToolbarItems:YES];
                              }];
}
```

效果:

![](http://upload-images.jianshu.io/upload_images/2648731-2e30d3f2c8cb81a0.gif?imageMogr2/auto-orient/strip)



#### 2. 逆时针旋转

```objective-c
- (IBAction)handleRotateCCW:(id)sender {
    [self enableToolbarItems:NO];
    [UIView animateKeyframesWithDuration:2.0
                                   delay:0.0
                                 options:UIViewKeyframeAnimationOptionCalculationModeLinear
                              animations:^{
          // 0 ~ 4π/3
          [UIView addKeyframeWithRelativeStartTime:0.0
                                  relativeDuration:1/3.0
                                        animations:^{
                                            self.rotatingImageView.transform = CGAffineTransformMakeRotation(4.0 * M_PI / 3.0);
                                        }];
          // 4π/3 ~ 2π/3
          [UIView addKeyframeWithRelativeStartTime:1/3.0
                                  relativeDuration:1/3.0
                                        animations:^{
                                            self.rotatingImageView.transform = CGAffineTransformMakeRotation(2.0 * M_PI / 3.0);
                                        }];
          // 2π/3 ~ 0
          [UIView addKeyframeWithRelativeStartTime:2/3.0
                                  relativeDuration:1/3.0
                                        animations:^{
                                            self.rotatingImageView.transform = CGAffineTransformMakeRotation(0);
                                        }];
                              }
                              completion:^(BOOL finished) {
                                  [self enableToolbarItems:YES];
                              }];
}
```

效果：

![](http://upload-images.jianshu.io/upload_images/2648731-c8c72779a633aaa9.gif?imageMogr2/auto-orient/strip)

---

The End.
