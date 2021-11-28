本文主要内容：分析卡片折叠动画的底层实现原理，并实现自定义示例。

![folding cell 折叠动画](https://upload-images.jianshu.io/upload_images/2648731-8655d495b0b028b6.gif?imageMogr2/auto-orient/strip)

[folding-cell](https://github.com/Ramotion/folding-cell) 是 GitHub 上的一个开源框架，它以纸卡折叠动画的方式实现了 iOS 中 UITableViewCell 的展开和折叠效果，⭐️ 数量将近 1w（截止 2019 年 10月），应该算是一个非常火的开源框架了。Folding Cell 的源码是以 **Swift** 语言实现的，本文主要讲述通过 **Objective-C** 语言的实现原理。


# 一、Core Animation 核心动画基础

注：本文仅涉及了与折叠动画相关的 Core Animation 核心动画基础知识，想要了解与 Core Animation 核心动画相关的更多内容，可以点击[这里](https://zsisme.gitbooks.io/ios-/content/)。


## 视图坐标系统

iOS 中的坐标系统：

以左上角为坐标原点，原点向右为 X 轴正方向，原点向下为 Y 轴正方向。

Z 轴分别与 X 轴和 Y 轴垂直，指向视角外为正方向。

坐标值由浮点数来表示。

![](https://upload-images.jianshu.io/upload_images/2648731-ce36718d822f6549.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 仿射变换

仿射变换的底层原理是 3x2 的数学矩阵运算。

仿射变换中的「仿射」指的是，无论变换矩阵用什么值，图层中平行的两条线在变换之后仍旧保持平行。

Core Animation 框架中的 `CATransform3D` 类型可以让图层在 3D 空间内移动，旋转或者缩放。

`CATransform3D` 是一个可以在三维空间内做变换的 4x4 的矩阵：

![](https://upload-images.jianshu.io/upload_images/2648731-80ebdd38f2d970fb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



我们可以通过 `CATransform3D` 中的方法对指定视图实现旋转、缩放和平移效果：

```objective-c
//  3D 旋转
CATransform3DMakeRotation(CGFloat angle, CGFloat x, CGFloat y, CGFloat z)
//  3D 缩放
CATransform3DMakeScale(CGFloat sx, CGFloat sy, CGFloat sz)
//  3D 平移
CATransform3DMakeTranslation(Gloat tx, CGFloat ty, CGFloat tz)
```

示例清单1：对视图内的图层绕 X 轴做 45˚的旋转。

```objective-c
// 绕 X 轴做 45 度角旋转的 3D 变换
CATransform3D transform3D = CATransform3DMakeRotation(M_PI_4, 1, 0, 0);
self.rotatedImageView.layer.transform = transform3D;
```

![](https://upload-images.jianshu.io/upload_images/2648731-071dc15115e2a018.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

结果：

左边的「静态视图」，即原图，右边的是绕 X 轴做 45˚ 旋转的视图。被旋转的视图看上去并没有被旋转，而是仅仅在水平方向上的一个压缩。视图看起来更窄的原因是：我们在二维平面视图中显示了一个三维视图，而且没有添加类似“远近”效果这个属性。换而言之，我们是用平视等距（即视图中的任意一个点与我们眼睛的距离都是相等的）的角度在看它，而不是透视的角度。



## 透视投影效果

可以通过修改 `CATransform3D` 矩阵来实现真实世界中物体「近大远小」的这一特性，以实现透视效果。

`CATransform3D` 的透视效果通过矩阵中的一个元素来控制：`m34`。`m34` 用于按比例缩放 X 和 Y 的值来计算到底要离视角多远。

![](https://upload-images.jianshu.io/upload_images/2648731-a55226e6bb5f3c4c.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`m34` 的默认值是 0，我们可以通过设置 `m34` 为 - 1.0 / `d` 来应用透视效果，`d` 代表了想象中视角相机和屏幕之间的距离，以像素为单位。

因为视角相机实际上并不存在，所以可以根据屏幕上的显示效果自由决定它放置的位置。通常 500-1000 就已经很好了，但对于特定的图层有时候更小或者更大的值会看起来更舒服，减少距离的值会增强透视效果，所以一个非常微小的值会让它看起来更加失真，然而一个非常大的值会让它基本失去透视效果。

示例清单2：为旋转视图添加透视效果。

```objective-c
// 创建一个新的 3D 单位矩阵变换
CATransform3D transform3D = CATransform3DIdentity;
// 添加透视效果
transform3D.m34 = - 1.0 / 500.0;
// 对视图内的图层绕 X 轴做 45 度角的旋转
transform3D = CATransform3DRotate(transform3D, M_PI_4, 1, 0, 0);
// 将变换添加到图层
self.rotatedImageView.layer.transform = transform3D;
```

![](https://upload-images.jianshu.io/upload_images/2648731-668e6ffe82fdbd55.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

结果：

🎉🎉🎉 我们实现了视图绕 X 轴方向做 45度角旋转的效果。但是你会发现，这个视图是围绕着视图的中心点在做旋转，也就是说，它的默认锚点是 {0.5, 0.5}，而卡片折叠所需要的效果是，让视图沿着上下两边做翻转，模拟现实世界中的折纸效果。这个问题我们后面会修复，现在，我们先将这个旋转变换添加到动画中去。



## 旋转动画

通过 `CALayer` 的添加动画方法  `addAnimation: forKey:`可以为视图的 `layer` 属性添加显式动画。

示例清单3：为视图添加旋转动画。

```objective-c
#pragma mark - Lifecycle

- (void)viewDidLoad {
    [super viewDidLoad];
    
    // 创建一个新的 3D 单位矩阵变换
    CATransform3D transform3D = CATransform3DIdentity;
    // 添加透视效果
    transform3D.m34 = - 1.0 / 500.0;
    // 将透视效果添加到图层
    self.rotatedImageView1.layer.transform = transform3D;
}

#pragma mark - IBActions

- (IBAction)rotateAnimation:(id)sender {
    
    // 添加属性动画，transform.rotation 是一个虚拟属性
    CABasicAnimation *animation = [CABasicAnimation animationWithKeyPath:@"transform.rotation.x"];
    // 为动画设置时间缓冲函数
    animation.timingFunction = [CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseOut];
    // 设置旋转角度为 90 度
    animation.toValue = @M_PI_2;
    // duration 表示单次动画的持续时间
    animation.duration = 2;
    animation.delegate = self;
    // 当动画结束后，layer 会一直保持着动画最后的状态
    // 效果：在动画执行过程中保留最后一帧，解决“回退”问题
    animation.fillMode = kCAFillModeForwards;
      /*
     一般说来，动画在结束之后被自动移除，除非设置 removedOnCompletion 为 NO，如果你设置动画在结束之后不被   
     自动移除，那么当它不需要的时候你要手动移除它；
     否则它会一直存在于内存中，直到图层被销毁。
     */
    animation.removedOnCompletion = NO;
    // 开始动画时间，默认为 0
    animation.beginTime = CACurrentMediaTime() + 0; 

    [self.rotatedImageView1.layer addAnimation:animation forKey:nil];
}

#pragma mark - CAAnimationDelegate

// 动画开始时执行
- (void)animationDidStart:(CAAnimation *)anim {
    NSLog(@"%s",__func__);
}

// 当一个完整动画执行完成或者执行动画的视图从父视图上被移除时执行
// 如果是一个完整动画执行完成后调用的这个方法，那么 flag 返回 true
- (void)animationDidStop:(CAAnimation *)anim finished:(BOOL)flag {
    NSLog(@"%s",__func__);
}
```

![实现旋转动画](https://upload-images.jianshu.io/upload_images/2648731-cf952691833383c6.gif?imageMogr2/auto-orient/strip)



### timingFunction 属性

`timingFunction` 属性用于定义动画执行的加速度，是匀速执行动画？还是先加速执行，再减速执行动画？...

通过设置 `timingFunction` 属性，可以让动画执行更平滑自然。

 `timingFunction` 属性是 `CAMediaTimingFunction` 类的一个对象，构造方法如下：

```objective-c
+ (instancetype)functionWithName:(CAMediaTimingFunctionName)name;
```

调用 `+timingFunctionWithName:` 构造方法可以创建 `CAMediaTimingFunction` 对象。可选的传入参数如下：

```objective-c
kCAMediaTimingFunctionLinear 选项创建了一个线性的计时函数，默认。
kCAMediaTimingFunctionEaseIn 选项创建了一个慢慢加速然后突然停止的方法
kCAMediaTimingFunctionEaseOut 以一个全速开始，然后慢慢减速停止。
kCAMediaTimingFunctionEaseInEaseOut 创建了一个慢慢加速然后再慢慢减速的过程。
kCAMediaTimingFunctionDefault 同上，但是加速和减速的过程都稍微有些慢
```

`CAMediaTimingFunction`  类创建的对象本质上是一个三次贝塞尔曲线：

![CAMediaTimingFunctionName](https://upload-images.jianshu.io/upload_images/2648731-95e37c638f14f2fc.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

综上所述，在卡片折叠动画中，真实场景下折叠打开纸张会存在空气阻力等影响动画速度的因素。

当打开 cell 时，动画速率应该是**先快后慢**，所以选择 `kCAMediaTimingFunctionEaseIn`。

当折叠 cell 时，动画速率应该是**先慢后快**，所以选择 `kCAMediaTimingFunctionEaseOut`。



### fillMode 属性

参考：[FillMode 详解](https://www.jianshu.com/p/91fccd32f6fb)

`fillMode`：决定当前对象在非 active 时间段的行为。比如动画开始之前，动画结束之后。

`fillMode` 属性是一个 `CAMediaTimingFillMode` 枚举类型，可选参数如下：

| CAMediaTimingFillMode  | 描述                                                         |
| ---------------------- | ------------------------------------------------------------ |
| `kCAFillModeForwards`  | 当动画结束后，layer 会一直保持着动画最后的状态。             |
| `kCAFillModeBackwards` | 当在动画开始前，你只要把 layer 加入到一个动画中，layer 便立即进入动画的初始状态并等待动画开始。 |
| `kCAFillModeBoth`      | 同时拥有以上类型的效果。                                     |
| `kCAFillModeRemoved`   | 默认值，也就是说当动画开始前和动画结束后，动画对 layer 都没有影响，动画结束后，layer 会恢复到之前的状态（可以理解为动画执行完成后移除）。 |



### 锚点

`anchorPoint` 属性是一个 `CGPoint` 类型的值，它默认位于图层的中点，即默认值为 {0.5, 0.5}。

而且 `anchorPoint` 的坐标值是一个单位坐标，即图层的左上角是 {0, 0}，右下角是 {1, 1}，因此默认的中心点坐标就是 {0.5, 0.5}。

图层的 `anchorPoint` 属性可以简单理解为用来移动图层的把柄。

示例清单4：设置图层的锚点。

```objc
#pragma mark - Lifecycle

- (void)viewDidLoad {
    [super viewDidLoad];
    
    // 创建一个新的 3D 单位矩阵变换
    CATransform3D transform3D = CATransform3DIdentity;
    transform3D.m34 = - 1.0 / 500.0;
    // 将透视效果添加到图层
    self.rotatedImageView1.layer.transform = transform3D;
    self.rotatedImageView2.layer.transform = transform3D;
    
    // 设置视图的锚点
    self.rotatedImageView2.layer.anchorPoint = CGPointMake(0.5, 1);
}

#pragma mark - IBActions

- (IBAction)rotateAnimation:(id)sender {
    
    CABasicAnimation *animation = [CABasicAnimation animationWithKeyPath:@"transform.rotation.x"];
    animation.timingFunction = [CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseOut];
    animation.toValue = @M_PI_2;
    animation.duration = 2;
    animation.delegate = self;
    animation.fillMode = kCAFillModeForwards;
    animation.removedOnCompletion = NO;
    animation.beginTime = CACurrentMediaTime() + 0; 

    [self.rotatedImageView1.layer addAnimation:animation forKey:nil];
    [self.rotatedImageView2.layer addAnimation:animation forKey:nil];
}
```

在 Interface Builder 中视图的位置约束是这样设置的：

![](https://upload-images.jianshu.io/upload_images/2648731-d02cca54bd9c4f17.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

说明：

左边视图位置在白线以上，锚点坐标是默认值{0.5, 0.5}，即蓝点标记的位置。

右边视图的位置下移了半个高度，同时锚点坐标我们改为了{0.5, 1}。

So，为啥要把右边的视图与原来的位置相比下调半个高度呢，因为设置锚点坐标为{0.5, 1}之后，视图的位置会沿 Y 轴向上移动 0.5 个单位坐标。也就是说，设置锚点坐标为{0.5, 1}之后，视图位置会自动向上平移半个高度，因此，我们要把视图的位置与理想中的位置下调半个高度，以抵消高度差。因此，当我们打开应用时，视图初始化显示的 UI 是这样的：

![](https://upload-images.jianshu.io/upload_images/2648731-a19e7bca67782dc5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



下面点击「旋转动画」按钮，查看效果：

![](https://upload-images.jianshu.io/upload_images/2648731-993f6c68a463bda0.gif?imageMogr2/auto-orient/strip)

注意观察，沿着 `transform.rotation.x` 旋转时，{0,  `M_PI_2` } 视图会顺时针从原始位置向屏幕里面转。

反过来，{`M_PI_2` , 0}  就是逆时针从屏幕里面往原始位置转，{0,  `-M_PI_2` } 即逆时针从原始位置往屏幕外面转。

粗略绘制的绕 X 轴方向旋转的角度图：

![](https://upload-images.jianshu.io/upload_images/2648731-3afbf31d197991f7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)





## 圆角

### 设置视图的圆角

`CALayer` 的 `conrnerRadius` 属性控制着图层角的曲率。它是一个浮点数，默认为 0（ 0 的时候就是直角），但是你可以把它设置成任意值。

 默认情况下，这个曲率值只影响背景颜色而不影响背景图片或者子图层。 不过，如果把 `masksToBounds` 设置成 `YES` ，整个图层（包括子图层）都会被圆角裁剪。

示例清单5：设置四个角为圆角。

```objective-c
// 设置图层的圆角半径
self.view1.layer.cornerRadius = 30.0f;

// masksToBounds: 沿着边界裁剪图形
// 当前视图层级下的所有子视图也会跟着被裁剪
self.view1.layer.masksToBounds = YES;
```

注：如果设置的圆角只会影响视图背景颜色，而不会影响到视图下的背景图片或者子图层的话，`masksToBounds` 属性可以不必设置，因为设置 `masksToBounds` 为 `YES` 时，会触发系统的离屏渲染，影响性能。



### 为视图的某一部分角设置圆角

`CAShapeLayer` 可以用来绘制所有能够通过 CGPath 来表示的形状。

通过 `CAShapeLayer` 画圆角的优势是可以单独指定每个角。

示例清单6：设置视图的左上角、右上角为圆角。

```objective-c
// 创建左上角和右上角是圆角的贝塞尔曲线路径
UIRectCorner rectCorner = UIRectCornerTopLeft | UIRectCornerTopRight;
UIBezierPath *path = [UIBezierPath bezierPathWithRoundedRect:self.view1.bounds
                                           byRoundingCorners:rectCorner
                                                 cornerRadii:CGSizeMake(30, 30)];

// 创建 CAShapeLayer 图层
CAShapeLayer *shapeLayer = [CAShapeLayer layer];
shapeLayer.path = path.CGPath;

// 设置图层蒙版
self.view2.layer.mask = shapeLayer;
```

示例清单7：设置视图的左下角、右下角为圆角。

```objective-c
// 创建左下角和右下角是圆角的贝塞尔曲线路径
UIRectCorner rectCorner = UIRectCornerBottomLeft | UIRectCornerBottomRight;
UIBezierPath *path = [UIBezierPath bezierPathWithRoundedRect:self.view3.bounds
                                           byRoundingCorners:rectCorner
                                                 cornerRadii:CGSizeMake(30, 30)];

// 创建 CAShapeLayer 图层
CAShapeLayer *shapeLayer = [CAShapeLayer layer];
shapeLayer.path = path.CGPath;

// 设置图层蒙版
self.view3.layer.mask = shapeLayer;
```

以上三个示例代码的效果如下：

![](https://upload-images.jianshu.io/upload_images/2648731-5dad9b31a759ebec.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)





# 二、折叠动画示例

示例清单8：最终要实现的两个折叠动画。

```objective-c
- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view from its nib.
    
    // 设置视图的锚点
    self.rotatedImageView1.layer.anchorPoint = CGPointMake(0.5, 1);
    
    // 创建一个新的 3D 单位矩阵变换
    CATransform3D transform3D = CATransform3DIdentity;
    // 添加透视效果
    transform3D.m34 = - 1.0 / 500.0;
    // 将透视效果添加到图层
    self.rotatedImageView1.layer.transform = transform3D;
}

#pragma mark - IBActions

// 打开
- (IBAction)rotateAnimation:(id)sender {
    
    // 添加属性动画，transform.rotation 是一个虚拟属性
    CABasicAnimation *animation = [CABasicAnimation animationWithKeyPath:@"transform.rotation.x"];
    // 为动画设置时间缓冲函数
    animation.timingFunction = [CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseIn];
    // 设置旋转角度为 -90 度
    animation.toValue = @- M_PI_2;
    // duration 表示单次动画的持续时间
    animation.duration = 2;
    animation.delegate = self;
    // 在动画执行过程中保留最后一帧，解决“回退”问题
    animation.fillMode = kCAFillModeForwards;
    /*
     一般说来，动画在结束之后被自动移除，除非设置 removedOnCompletion 为 NO，如果你设置动画在结束之后不被自动移除，那么当它不需要的时候你要手动移除它；
     否则它会一直存在于内存中，直到图层被销毁。
     */
    animation.removedOnCompletion = NO;
    animation.beginTime = CACurrentMediaTime() + 0; // 开始动画时间，默认为 0

    [self.rotatedImageView1.layer addAnimation:animation forKey:nil];
}

// 折叠
- (IBAction)foldingAnimation:(id)sender {
    // 添加属性动画，transform.rotation 是一个虚拟属性
    CABasicAnimation *animation = [CABasicAnimation animationWithKeyPath:@"transform.rotation.x"];
    // 为动画设置时间缓冲函数
    animation.timingFunction = [CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseOut];
    // 设置旋转角度为 90 度
    animation.toValue = @ M_PI_2;
    // duration 表示单次动画的持续时间
    animation.duration = 2;
    animation.delegate = self;
    // 在动画执行过程中保留最后一帧，解决“回退”问题
    animation.fillMode = kCAFillModeForwards;
    /*
     一般说来，动画在结束之后被自动移除，除非设置 removedOnCompletion 为 NO，如果你设置动画在结束之后不被自动移除，那么当它不需要的时候你要手动移除它；
     否则它会一直存在于内存中，直到图层被销毁。
     */
    animation.removedOnCompletion = NO;
    animation.beginTime = CACurrentMediaTime() + 0; // 开始动画时间，默认为 0

    [self.rotatedImageView1.layer addAnimation:animation forKey:nil];
    
}

#pragma mark - CAAnimationDelegate

// 动画开始时执行
- (void)animationDidStart:(CAAnimation *)anim {
    NSLog(@"%s",__func__);
    self.rotatedImageView1.hidden = NO;
}

// 当一个完整动画执行完成或者执行动画的视图从父视图上被移除时执行
// 如果是一个完整动画执行完成后调用的这个方法，那么 flag 返回 true
- (void)animationDidStop:(CAAnimation *)anim finished:(BOOL)flag {
    NSLog(@"%s",__func__);
    
    // 动画结束后，移除所有添加到图层上的动画
    [self.rotatedImageView1.layer removeAllAnimations];
    self.rotatedImageView1.hidden = YES;
}
```

![](https://upload-images.jianshu.io/upload_images/2648731-3e1adff8c81ee267.gif?imageMogr2/auto-orient/strip)



# 三、Folding Cell 折叠动画原理解析

## Folding Cell 包含的UI元素

首先，我们需要创建一个 `UITableViewCell` 的实例 `FoldingCell`，然后通过 Interface Builder 的方式设置这个 Cell 元素中包含的 UI 元素：

![](https://upload-images.jianshu.io/upload_images/2648731-0fe1e7e1a03ccc85.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


第一个子视图是 Foreground View 前景视图，它是当 Cell 被折叠起来时的默认显示内容。

Foreground View 是 RotatedView 类型，它是可以执行翻转动画的。

![](https://upload-images.jianshu.io/upload_images/2648731-8a0b71ecfc6e70ed.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



第二个子视图是 Container View 容器视图，它是当 Cell  被展开时显示的内容，它包含四个子视图（Background SubView 1，Background SubView 2，Background SubView 3，Background SubView 4）。

其中，SubView 1 是 UIView 类型，它不可以执行翻转，因为当 Foreground View 前景视图翻转打开时，SubView 1 就显示出来了，没有必要执行翻转动画。

其中，SubView 2、SubView 3、SubView 4 是 RotatedView 类型，它是可以执行翻转动画的。
![](https://upload-images.jianshu.io/upload_images/2648731-78161c31fabab9bd.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


Foreground View 前景视图是如何转变成为 Container View 容器视图的呢？

当页面初始化时，需要通过代码调整 Container View 容器离顶部的距离，将它调整到和 Foreground View 前景视图重叠，通过设置这两个视图的 `hidden` 属性来显示或隐藏 UI，再加一点点过渡动画。



### 反面视图

为了让折叠动画更具有真实性，我们还为部分视图添加了背面视图，模拟纸张的反面（用纯色背景视图代替）。

需要注意到，在子视图 2 上添加的是子视图 3 的反面。在子视图 3 上添加的是子视图 4 的反面。

![](https://upload-images.jianshu.io/upload_images/2648731-b052f0e4640bd288.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


查看视图的 UI 层次结构（如下图所示），背景视图就是显示在正常子视图上面的一个遮罩视图，也是通过 `hidden` 属性来控制它的显示与隐藏的。

另外这个 backView 也是 RotatedView 类型，它也可以执行翻转动画。

![](https://upload-images.jianshu.io/upload_images/2648731-35da42fb6b8e6e0b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)





## 展开动画原理

我们把展开动画的执行过程放慢之后，录屏显示仔细观察如下所示：

![](https://upload-images.jianshu.io/upload_images/2648731-b06fe19cfd426292.gif?imageMogr2/auto-orient/strip)



动画执行过程：

1. Foreground View：前景视图做 （0，-M_PI_2）旋转，转到 -M_PI_2 位置时隐藏。
2. SubView 1：因为前景视图做完翻转动画后，子视图 1 就直接显示出来了，所以无需执行动画。
3. SubView 2: 子视图带着附在它上面的 backView 一起做（M_PI_2，0）旋转，转到 0 位置。
4. SubView 2 上的 backView：做（0，-M_PI_2）旋转，转到 -M_PI_2 位置时隐藏。
5. SubView 3: 子视图带着附在它上面的 backView 一起做（M_PI_2，0）旋转，转到 0 位置。
6. SubView 3 上的 backView：做（0，-M_PI_2）旋转，转到 -M_PI_2 位置时隐藏。
7. SubView 4: 做（M_PI_2，0）旋转，转到 0 位置，（注意，它没有 backView，因为它是最后一个视图了，翻转结束就显示自身内容）。



## 折叠动画原理

![](https://upload-images.jianshu.io/upload_images/2648731-ae12a81f376f6e61.gif?imageMogr2/auto-orient/strip)



折叠动画的动画执行过程就是展开动画反向执行一次：

1. SubView 4: 做（0，M_PI_2）旋转，转到 M_PI_2 位置，即与页面垂直位置就隐藏。
2. SubView 3 上的 backView：做（-M_PI_2，0）旋转，转到 0 位置。
3. SubView 3: 子视图带着附在它上面的 backView 一起做（0，M_PI_2）旋转，转到 M_PI_2 位置就隐藏。
4. SubView 2 上的 backView：做（-M_PI_2，0）旋转，转到 0 位置，不隐藏。
5. SubView 2: 子视图带着附在它上面的 backView 一起做（0，M_PI_2）旋转，转到 M_PI_2 位置隐藏。
6. Foreground View：前景视图做 （-M_PI_2，0）旋转，转到 0 位置。



## 源码实现

实现源码可见：<https://github.com/Andy0570/CoreAnimationDemo>，源码的每一个方法、属性基本上都有中文注释，感兴趣的同学建议配合源码注释，另外创建一个新的项目亲自动手实现一遍才好。

> 千里之行，始于足下。



# 参考

* [iOS Core Animation: Advanced Techniques 中文译本](https://zsisme.gitbooks.io/ios-/content/)
* [GitHub 源码：folding-cell](https://github.com/Ramotion/folding-cell)
* [GitHub 源码：foldingCell-oc](https://github.com/charsdavy/foldingCell-oc)
* [GitHub 源码：PP_FoldingCell](https://github.com/L-vinCent/PP_FoldingCell)



