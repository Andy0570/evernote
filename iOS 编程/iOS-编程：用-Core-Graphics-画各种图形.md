![](http://upload-images.jianshu.io/upload_images/2648731-3dcdf1527bdf692f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/620) 

## 概述
* 无论绘制 JPEG、PDF 还是视图的图层，都是由 **Core Graphics** 框架完成的。
* **Core Graphics** 中最重要的“对象”是图形上下文（graphics context）,图形上下文是 **CGContextRef** 的“对象”，负责存储绘画状态（例如画笔颜色和线条粗细）和绘制内容所处的内存空间。

> 注：
 带有 **Ref** 后缀的类型是 **Core Graphics** 中用来模拟面向对象机制的C结构。
带有 **Ref** 后缀类型的对象可能具有指向其他 **Core Graphics** “对象”的强引用指针，并成为这些“对象”的拥有者。但是ARC无法识别这类强引用和“对象”所有权，必须在使用完成之后手动释放。
💡 规则：如果使用名称中带有create 或者 copy 的函数创建了一个 **Core Graphics** “对象”，就必须调用对应的 `Release` 函数并传入该对象的指针。


### `drawRect:`
* 视图的 `drawRect：`方法在执行之前，系统首先为视图的图层创建一个图形上下文，然后为绘画状态设置一些默认参数。
*  `drawRect：`方法开始执行时，随着图形上下文不断执行绘画操作，图层上的内容也会随之改变。
*  `drawRect：`方法执行完毕后，系统会将图层与其他图层一起组合完成完整的图像并显示在屏幕上。

视图会根据 `drawRect：`方法将自己绘制到图层上，UIView 的子类可以覆盖 `drawRect：`方法，完成自定义的绘图任务。

## 画圆
使用 **CGContext**
        
### 边框圆

	//一个不透明的Quartz 2D绘画环境，相当于一个画布
    CGContextRef context = UIGraphicsGetCurrentContext();

    CGContextSetRGBStrokeColor(context, 1, 0, 0, 1);//画笔的颜色
    
    CGContextSetLineWidth(context, 1.0);//线宽
    
    CGContextAddArc(context,
                    100, 20, 15, 0, M_PI * 2.0, 0);//添加圆
                    
    CGContextDrawPath(context, kCGPathStroke);//绘制路径
    
    
### 边框圆（方法与上类似，待修改完善）

	CGContextSetRGBStrokeColor(context, 1, 0, 0, 1);
	
    CGMutablePathRef path = CGPathCreateMutable();
    
    //创建用于转移坐标的transform
    CGAffineTransform trandform =CGAffineTransformMakeTranslation(80, 40);
    
    CGPathAddArc(path, &trandform, 80, 40, 25, 0, M_PI * 2.0, YES);
   
    CGContextAddPath(context, path);
    
    CGContextDrawPath(context, kCGPathStroke);
       
    CGPathRelease(path);
    
###  填充圆，无边框

    CGContextAddArc(context, 150, 30, 30, 0, M_PI * 2.0, 0);//添加一个圆
    
    CGContextDrawPath(context, kCGPathFill);//仅填充
    
### 画边框圆，并填充圆

    UIColor *myColor = [UIColor colorWithRed:1 green:0 blue:0 alpha:1];
    
    CGContextSetFillColorWithColor(context, myColor.CGColor);//填充颜色
    
    CGContextSetLineWidth(context, 3.0);//线宽
    
    CGContextAddArc(context, 250, 40, 40, 0, M_PI * 2.0, 0);//添加一个圆
    
    CGContextDrawPath(context, kCGPathFillStroke);//绘制路径并填充
    
### 使用 UIBezierPath（贝塞尔曲线）绘制圆形
* **UIBezierPath** 和 **UIColor** 的所有绘图功能都可以通过直接调用 **Core Graphics** 函数完成。
* **UIBezierPath** 类，其实是将 **Core Graphics**代码封装在了一系列方法中，以便开发者调用，降低了绘图的难度。
* 但是有些功能只能使用 **Core Graphics** 完成，如：绘制渐变。


```
// 设置圆心    
CGPoint centre = CGPointMake(50, 60);
 
// 设置半径   
float radius = 25;

//  UIBezierPath 用来绘制直线或曲线，从而组成各种形状   
UIBezierPath *bezierPath = [[UIBezierPath alloc]init];

// 定义路径：以中心点为圆心、radious的值为半径定义一个0到M_PI*2.0弧度的路径（整圆)
[bezierPath addArcWithCenter:centre
                          radius:radius
                      startAngle:0.0
                        endAngle:PI * 2.0
                       clockwise:YES];

// 设置线条宽度
bezierPath.lineWidth = 3;

// 设置线条颜色
[[UIColor lightGrayColor] setStroke];

// 绘制路径
[bezierPath stroke];
```

    
##### 绘制同心圆

	//获取当前画板边界
    CGRect bounds=self.bounds;
    
    //根据bounds计算中心点
    CGPoint center;
    center.x = bounds.origin.x + bounds.size.width / 2.0;
    center.y = bounds.origin.y + bounds.size.height / 2.0;
    
    //使最外层圆形成为视图的外接圆
    //使用视图的对角线作为最外层圆形的直径
    float maxRadius = hypot(bounds.size.width, bounds.size.height)/ 2.0;
    
    UIBezierPath *path = [[UIBezierPath alloc] init];
    
    for (float currentRadius = maxRadius;
         currentRadius>0;
         currentRadius -=20)
    {
        //每次绘制新圆前，抬笔，重置起始点
        [path moveToPoint:CGPointMake(center.x +currentRadius, center.y)];
        
        [path addArcWithCenter:center
                        radius:currentRadius
                    startAngle:0.0
                      endAngle:M_PI * 2.0
                     clockwise:YES];
    }
    
    //设置线条宽度为10点
    path.lineWidth = 10;
    
    //设置绘制颜色为浅灰色
    [[UIColor lightGrayColor] setStroke];
     
    //绘制路径
    [path stroke];
    


---


### 画线及弧线
##### 画线
    CGPoint point[3];
    point[0] = CGPointMake(100, 120);
    point[1] = CGPointMake(130, 120);
    point[2] = CGPointMake(120, 100);
    
	//CGContextAddLines(<#CGContextRef  _Nullable c#>, <#const CGPoint * _Nullable points#>, <#size_t count#>)// （context画布，坐标数组，坐标数）
	CGContextAddLines(context, point, 3);//添加线
	
    CGContextDrawPath(context, kCGPathStroke);//根据坐标绘制路径
    
##### 画笑脸弧线

    //左眼
    CGContextSetRGBStrokeColor(context, 0, 0, 1, 1);//改变画笔颜色
    
    CGContextMoveToPoint(context, 140, 120);//开始坐标p1
    
    //CGContextAddArcToPoint(context, <#CGFloat x1#>, <#CGFloat y1#>, <#CGFloat x2#>, <#CGFloat y2#>, <#CGFloat radius#>)
    //开始坐标p1与（x1,y1）相连,（x1,y1）与（x2,y2）相连
    CGContextAddArcToPoint(context, 150, 110, 160, 120, 20);
    
    CGContextStrokePath(context);
    
    //右眼
    CGContextMoveToPoint(context, 180, 120);
    CGContextAddArcToPoint(context, 190, 110, 200, 120, 20);
    CGContextStrokePath(context);
    
    //嘴巴
    CGContextMoveToPoint(context, 160, 150);
    CGContextAddArcToPoint(context, 170, 160, 180, 150, 20);
    CGContextStrokePath(context);

---
### 画矩形
##### 画矩形框
    CGContextStrokeRect(context, CGRectMake(100, 200, 50, 20));
    
##### 画填充块    
    CGContextFillRect(context, CGRectMake(160, 200, 50, 20));
    
##### 画矩形，并填充颜色   
    
    UIColor *myRectColor = [UIColor blueColor];
    CGContextSetFillColorWithColor(context, myRectColor.CGColor);//填充颜色
    
    UIColor *myRectFrameColor = [UIColor redColor];
    CGContextSetStrokeColorWithColor(context, myRectFrameColor.CGColor);//线框颜色
    
    CGContextAddRect(context, CGRectMake(100, 230, 100, 50));//画框
    CGContextSetLineWidth(context, 2.0);//线宽
    CGContextDrawPath(context, kCGPathFillStroke);//绘画路径
    

##### 画矩形，并填充渐变色
    
    CGContextSaveGState(UIGraphicsGetCurrentContext());
    
    //设置渐变
    CGFloat locations [2] ={0.0,1.0};
    CGFloat components[8] ={1.0,0.0,0.0,1.0,    //起始颜色为红色
        0.0,1.0,0.0,1.0};   //终止颜色为黄色
    
    //色彩范围容器
    CGColorSpaceRef colorspace = CGColorSpaceCreateDeviceRGB();
    
    //渐变属性：颜色空间、颜色、位置、有效数量
    //CGGradientCreateWithColorComponents:创建包含渐变的CGGradientRef对象
    CGGradientRef gradient = CGGradientCreateWithColorComponents(colorspace, components, locations, 2);
    
    //画矩形
    CGContextMoveToPoint(context, 250, 230);
    CGContextAddLineToPoint(context, 350, 230);
    CGContextAddLineToPoint(context, 350, 280);
    CGContextAddLineToPoint(context, 250, 280);
    CGContextClip(context);//裁剪路径
    
    //控制渐变的方向和形状
    CGPoint startPoint = CGPointMake(250,230);
    CGPoint endPoint = CGPointMake(350,280);
    
    //绘制线性渐变
    CGContextDrawLinearGradient(context, gradient, startPoint, endPoint, kCGGradientDrawsAfterEndLocation);
    
    CGGradientRelease(gradient);
    CGColorSpaceRelease(colorspace);
    
    //恢复Graphical Context图形上下文
    CGContextRestoreGState(UIGraphicsGetCurrentContext());

---

### 画扇形和椭圆
##### 画扇形就是画圆，只不过设置角度的大小，形成一个扇形

    UIColor *color1 = [UIColor colorWithRed:227 green:237 blue:205 alpha:1];
    CGContextSetFillColorWithColor(context, color1.CGColor);//填充颜色
    
    //以10为半径围绕圆形画指定角度的扇形
    CGContextMoveToPoint(context, 40, 380);
    CGContextAddArc(context, 40, 380, 40, -60 * M_PI /180, - 120 * M_PI /180, 1);
    CGContextClosePath(context);//路径结束标志
    
    CGContextDrawPath(context, kCGPathFillStroke);//绘制路径
  
---

##### 画椭圆

    CGContextAddEllipseInRect(context, CGRectMake(100, 340, 40, 20));
    
    CGContextDrawPath(context, kCGPathFillStroke);
    
---

### 画三角形
    //只要三个点就行，跟画一条线方式一样，把三点连起来
    CGPoint triangle[3];//坐标数组
    triangle[0] = CGPointMake(60, 430);
    triangle[1] = CGPointMake(20, 470);
    triangle[2] = CGPointMake(80, 480);
    
    CGContextAddLines(context, triangle, 3);//添加线
    CGContextClosePath(context);//封闭路径
    
    UIColor *fillcolor = [UIColor brownColor];
    CGContextSetFillColorWithColor(context, fillcolor.CGColor);//填充色
    
    CGContextDrawPath(context, kCGPathFillStroke);//根据坐标绘制路径并填充
    
### 绘制三角形
    //获得当前画板
	CGContextRef context = UIGraphicsGetCurrentContext();
    
    CGRect bounds = self.bounds;
    
    //标记
    CGContextBeginPath(context);
    
    //设置起点
    CGContextMoveToPoint(context,bounds.size.width / 2.0,bounds.size.height /2.0+70);
    CGContextAddLineToPoint(context, bounds.size.width / 2.0 - 60, bounds.size.height / 2.0 +120);
    CGContextAddLineToPoint(context, bounds.size.width / 2.0 + 60, bounds.size.height / 2.0 +120);
    //路径结束标志
    CGContextClosePath(context);
    
    //设置边框颜色
    [[UIColor grayColor] setStroke];
    //绘制路径
    CGContextDrawPath(context, kCGPathStroke);
    
---

### 画圆角矩形
    float fw = 160;
    float fh = 500;
    
    CGContextMoveToPoint(context, fw+20, fh);//开始坐标，右边开始
    
    //圆弧与(x1,y1)(x2,y2)的直线相切
	//CGContextAddArcToPoint(<#CGContextRef  _Nullable c#>, 
									<#CGFloat x1#>, 
									<#CGFloat y1#>, 
									<#CGFloat x2#>, 
									<#CGFloat y2#>, 
									<#CGFloat radius#>); 
								
    CGContextAddArcToPoint(context, fw, fh, fw, fh+20, 10);//左上角角度
    CGContextAddArcToPoint(context, fw, 600, fw+20, 600, 10);//左下角角度
    CGContextAddArcToPoint(context, 280, 600, 280, 580, 10);//右下角
    CGContextAddArcToPoint(context,280, fh, 260, fh, 10);//右上角
    CGContextClosePath(context);//封闭路径
    CGContextDrawPath(context, kCGPathStroke);//根据坐标绘制路径
    

---
### 画贝塞尔曲线
##### 二次曲线
    CGContextMoveToPoint(context, 90, 630);//设置path的起点
    
    //设置贝塞尔曲线的控制点坐标(cpx,cpy)和终点坐标(x,y)
    //CGContextAddQuadCurveToPoint(<#CGContextRef  _Nullable c#>, <#CGFloat cpx#>, <#CGFloat cpy#>, <#CGFloat x#>, <#CGFloat y#>);
    CGContextAddQuadCurveToPoint(context, 90, 720, 150, 630);
    
    CGContextStrokePath(context);
    
##### 三次曲线函数
    CGContextMoveToPoint(context, 180, 630);//设置path起点
    
	//CGContextAddCurveToPoint(<#CGContextRef  _Nullable c#>, <#CGFloat cp1x#>, <#CGFloat cp1y#>, <#CGFloat cp2x#>, <#CGFloat cp2y#>, <#CGFloat x#>, <#CGFloat y#>)
    CGContextAddCurveToPoint(context, 225, 570, 225, 690, 270, 630);
    CGContextStrokePath(context);
    
---

### 图片
    UIImage *myImage =[UIImage imageNamed:@"monk.jpg"];
    [myImage drawInRect:CGRectMake(80, 680, 80, 56)];
    
    //保持图片大小在point点开始画图
    [myImage drawAtPoint:CGPointMake(0, 0)];
    
    //使图片上下颠倒
    CGContextDrawImage(context, CGRectMake(200, 680, 80, 56), myImage.CGImage);
    
    //平铺图    
    CGContextDrawTiledImage(context, CGRectMake(<#CGFloat x#>, <#CGFloat y#>, <#CGFloat width#>, <#CGFloat height#>), <#CGImageRef  _Nullable image#>);


---
### 为图片添加阴影

* 绘制阴影之前，需要将阴影效果添加到一个图形上下文中，之后在该图形上下文中绘制的所有不透明图片都会带有阴影效果。
* 复写父类 **UIView** 的 ``drawRect:`` 方法，绘制自定义图形：

```

    // 保存绘图状态
    CGContextSaveGState(UIGraphicsGetCurrentContext());
   
    // 添加阴影：设置阴影偏移量、模糊指数
    CGContextSetShadow(UIGraphicsGetCurrentContext(), CGSizeMake(4, 7), 2);
    
    // 创建UIImage对象
    UIImage *logoImage2 = [UIImage imageNamed:@"logo.png"];
    
    // 将图像绘制到视图
    CGRect logoBounds = CGRectMake(bounds.size.width/2.0-100, bounds.size.height/2.0-140, 200, 280);
    [logoImage2 drawInRect:logoBounds];
    
    // 恢复绘图状态
    CGContextRestoreGState(UIGraphicsGetCurrentContext());
```

---
### 渐变

```

	//设置渐变
    CGContextSaveGState(UIGraphicsGetCurrentContext());
    
    CGFloat locations [2] ={0.0,1.0};
    CGFloat components[8] ={1.0,0.5,0.0,1.0,    //起始颜色为红色
        0.0,1.0,1.0,1.0};   //终止颜色为黄色
    
    //色彩范围容器
    CGColorSpaceRef colorspace = CGColorSpaceCreateDeviceRGB();
    
    //渐变属性：颜色空间、颜色、位置、有效数量
    //CGGradientCreateWithColorComponents:创建包含渐变的CGGradientRef对象
    CGGradientRef gradient = CGGradientCreateWithColorComponents(colorspace, components, locations, 2);
    
    CGPoint startPoint = CGPointMake(0,0);
    CGPoint endPoint = CGPointMake(bounds.size.width,bounds.size.height);
    
    //绘制线性渐变
    CGContextDrawLinearGradient(UIGraphicsGetCurrentContext(), gradient, startPoint, endPoint, 0);
    
    CGGradientRelease(gradient);
    CGColorSpaceRelease(colorspace);
    
    //恢复Graphical Context图形上下文
    CGContextRestoreGState(UIGraphicsGetCurrentContext());
```

```
// 设置表头视图背景渐变
- (void)setTableHeaderViewBackgroundGradient {
    // 创建 CAGradientLayer 对象
    CAGradientLayer *gradient = [CAGradientLayer layer];
    // 设置 CAGradientLayer 对象的 frame
    gradient.frame = self.tableHeaderView.frame;
    // 创建渐变色数组，需要转换为 CGColor 颜色
    gradient.colors = @[ (id)[UIColor colorWithRed:62 /255.0f
                                             green:145/255.0f
                                              blue:255/255.0f
                                             alpha:1.0].CGColor,
                         (id)[UIColor colorWithRed:73 /255.0
                                             green:196/255.0
                                              blue:255/255.0
                                             alpha:1].CGColor];
    // 设置三种颜色变化点，取值范围 0.0~1.0
    gradient.locations = @[@(0.1f),@(1.0f)];
    // 设置渐变颜色方向
    gradient.startPoint = CGPointMake(0, 0);
    gradient.endPoint = CGPointMake(1, 0);
    // 添加渐变到 UIView 上
    [self.tableHeaderView.layer insertSublayer:gradient atIndex:0];
}
```

### 在 UIView 子类对象上画一条线

覆盖子类 `drawRect:`方法：
```
// 在用户名和密码之间添加线
- (void)drawRect:(CGRect)rect {
    // 获得当前画板
    CGContextRef context = UIGraphicsGetCurrentContext();
    // 设置线宽
    CGContextSetLineWidth(context, 0.2);
    // 标记
    CGContextBeginPath(context);
    // 路径起点
    CGContextMoveToPoint(context, 10, 50);
    // 路径终点
    CGContextAddLineToPoint(context, self.frame.size.width - 10, 50);
    // 路径结束标志
    CGContextClosePath(context);
    // 设置画线颜色
    [[UIColor grayColor] setStroke];
    // 绘制路径
    CGContextStrokePath(context);
}
```


### CAShapeLayer

> 摘自：[内存恶鬼drawRect](http://bihongbo.com/2016/01/03/memoryGhostdrawRect/) 

**CAShapeLayer** 是一个通过**矢量图形**而不是 `bitmap` 来绘制的图层子类。用 `CGPath` 来定义想要绘制的图形，**CAShapeLayer** 会自动渲染。它可以完美替代我们的直接使用 **Core Graphics** 绘制 `layer`，对比之下使用**CAShapeLayer** 有以下优点：

- 渲染快速。**CAShapeLayer** 使用了硬件加速，绘制同一图形会比用 Core Graphics 快很多。
- 高效使用内存。一个 **CAShapeLayer** 不需要像普通 CALayer 一样创建一个寄宿图形，所以无论有多大，都不会占用太多的内存。
- 不会被图层边界剪裁掉。
- 不会出现像素化。

```objective-c
CAShapeLayer *brownRectLayer = [CAShapeLayer layer];
brownRectLayer.frame = CGRectMake(0, 0, self.view.frame.size.width, self.view.frame.size.height);
UIBezierPath *path = [UIBezierPath bezierPathWithRect:CGRectMake(0, 0, self.view.frame.size.width, self.view.frame.size.height)];
brownRectLayer.path = path.CGPath;
brownRectLayer.fillColor = [UIColor brownColor].CGColor;
[self.view.layer addSublayer:brownRectLayer];

CAShapeLayer *whiteRectLayer = [CAShapeLayer layer];
whiteRectLayer.frame = CGRectMake(0, 0, self.view.frame.size.width, self.view.frame.size.height);
UIBezierPath *path1 = [UIBezierPath bezierPathWithRect:CGRectMake(self.view.frame.size.width / 2 - 25, self.view.frame.size.height / 2 - 25, 50, 50)];
whiteRectLayer.path = path1.CGPath;
whiteRectLayer.fillColor = [UIColor whiteColor].CGColor;
[self.view.layer addSublayer:whiteRectLayer];
```

![](http://upload-images.jianshu.io/upload_images/2648731-8c1cf2356ae2e4e0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 参考

* [IOS用CGContextRef画各种图形](http://blog.csdn.net/rhljiayou/article/details/9919713)
* [Core Graphics-绘图使用介绍](http://www.jianshu.com/p/8c2bda98ad6e)
