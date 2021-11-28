# UIView

### 1. UIView视图的基本使用

在根视图里添加两个子视图；

```objective-c
CGRect rect1 = CGRectMake(30, 50, 200, 200);
UIView *view1 = [[UIView alloc] initWithFrame:rect1];
[view1 setBackgroundColor:[UIColor brownColor]];

CGRect rect2 = CGRectMake(60, 90, 200, 200);
UIView *view2 = [[UIView alloc] initWithFrame:rect2];
[view2 setBackgroundColor:[UIColor purpleColor]];
[view2 setAlpha:0.5];

[self.view addSubview:view1];
[self.view addSubview:view2];
```

### 2. UIView视图的层次关系

创建三个UIView视图，第一个视图是第二个视图的父视图，第二个视图是第三个视图的父视图：

```objective-c
UIView *view1 = [[UIView alloc] initWithFrame:CGRectMake(20, 80, 280, 280)];
view1.backgroundColor = [UIColor redColor];
[self.view addSubview:view1];

UIView *view2 = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 200, 200)];
// setBounds 方法，用来设置视图本地坐标系统中的位置和大小，他会影响子视图的位置和显示
[view2 setBounds:CGRectMake(-40, -20, 200, 200)];
view2.backgroundColor = [UIColor yellowColor];
[view1 addSubview:view2];

UIView *view3 = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 100, 100)];
view3.backgroundColor = [UIColor blueColor];
[view2 addSubview:view3];
```

### 3. UIView视图的基本操作

实现视图的添加与删除，以及切换视图在父视图中的层次：
💡💡💡
* `bringSubviewToFront:`
* `sendSubviewToBack:`


```objective-c
- (void)viewDidLoad {
    [super viewDidLoad];
    
    // 3.UIView视图的基本操作
    CGRect rect = CGRectMake(30, 50, 200, 200);
    UIView *view = [[UIView alloc] initWithFrame:rect];
    [view setBackgroundColor:[UIColor brownColor]];
    [self.view addSubview:view];
    
    // 创建一个按钮，当点击按钮时，将动态添加另一个视图
    UIButton *btAdd = [[UIButton alloc] initWithFrame:CGRectMake(30, 350, 80, 30)];
    [btAdd setBackgroundColor:[UIColor grayColor]];
    [btAdd setTitle:@"Add" forState:UIControlStateNormal];
    [btAdd addTarget:self action:@selector(addView) forControlEvents:UIControlEventTouchUpInside];
    [self.view addSubview:btAdd];
    
    // 创建第二个按钮，当点击这个按钮时，将切换根视图中，两个视图的层次结构
    UIButton *btBack = [[UIButton alloc] initWithFrame:CGRectMake(120, 350, 80, 30)];
    [btBack setBackgroundColor:[UIColor grayColor]];
    [btBack setTitle:@"Switch" forState:UIControlStateNormal];
    [btBack addTarget:self action:@selector(bringViewToBack) forControlEvents:UIControlEventTouchUpInside];
    [self.view addSubview:btBack];
    
    // 创建第三个按钮，当点击这个按钮时，将从前窗口的根视图中，删除新添加的视图。
    UIButton *btRemove = [[UIButton alloc] initWithFrame:CGRectMake(210, 350, 80, 30)];
    [btRemove setBackgroundColor:[UIColor grayColor]];
    [btRemove setTitle:@"Remove" forState:UIControlStateNormal];
    [btRemove addTarget:self action:@selector(removeView) forControlEvents:UIControlEventTouchUpInside];
    [self.view addSubview:btRemove];
}

#pragma mark - IBAction
#pragma mark 按钮1
- (void)addView {
    CGRect rect = CGRectMake(60, 90, 200, 200);
    UIView *view = [[UIView alloc] initWithFrame:rect];
    [view setBackgroundColor:[UIColor purpleColor]];
    [view setTag:1];
    
    [self.view addSubview:view];
}

#pragma mark 按钮2
- (void)bringViewToBack {
    // 通过标签值，找到新添加的视图
    UIView *view =[self.view viewWithTag:1];
    // 将当前的视图，移到所有兄弟视图的下方
    [self.view sendSubviewToBack:view];
}

#pragma mark 按钮3
- (void)removeView {
    UIView *view = [self.view viewWithTag:1];
    [view removeFromSuperview];
}

```

### 4. UIView视图添加边框效果

视图本身，更像是一个图层的管理器，访问它的跟绘图和坐标有关的属性，实际上都是在访问它所包含的图层的相关属性。

给一张图片添加一个彩色相框：

```
UIImage *image = [UIImage imageNamed:@"github"];
UIImageView *imageView = [[UIImageView alloc] initWithImage:image];
imageView.frame = CGRectMake(60, 60, 200, 200);

// 设置图片视图的图层边框宽度、颜色
imageView.layer.borderWidth = 10;
imageView.layer.borderColor = [[UIColor purpleColor] CGColor];

[self.view addSubview:imageView];
```

### 5. UIView视图添加圆角效果

给矩形图片添加圆角效果：

```objective-c
UIImage *image = [UIImage imageNamed:@"woman"];
UIImageView *imageView = [[UIImageView alloc] initWithImage:image];
imageView.frame = CGRectMake(60, 60, 300, 170);

// 设置图像视图的层的圆角半径大小
imageView.layer.cornerRadius = 50;
// 设置图像视图的层的遮罩覆盖属性，进行边角裁切。
imageView.layer.masksToBounds = YES;

[self.view addSubview:imageView];
```

开发中给 **Button** 设置圆角也可以使用这个方法。

### 6. UIView视图添加阴影效果

给矩形图片添加阴影效果:

```
UIImage *image = [UIImage imageNamed:@"woman"];
UIImageView *imageView = [[UIImageView alloc] initWithImage:image];
imageView.frame = CGRectMake(60, 60, 300, 170);

// 设置图像视图层的阴影颜色
imageView.layer.shadowColor = [[UIColor blackColor] CGColor];
// 设置图像视图层的阴影横向和纵向的偏移量
imageView.layer.shadowOffset = CGSizeMake(5.0f, 5.0f);
// 设置图像视图层的阴影透明度
imageView.layer.shadowOpacity = 0.75;
// 设置图像视图层的阴影半径大小
imageView.layer.shadowRadius = 10.0f;

[self.view addSubview:imageView];
```

### 7. UIView视图的渐变填充

创建一个渐变图形:

```objective-c
CGRect rect = CGRectMake(60, 120, 200, 200);
UIView *view = [[UIView alloc] initWithFrame:rect];
view.backgroundColor = [UIColor whiteColor];

// 新建一个渐变层
CAGradientLayer *gradientLayer = [CAGradientLayer layer];
// 设置渐变层的位置和尺寸，与视图对象一致
gradientLayer.frame = view.bounds;

// 设置渐变的起始颜色为蓝色
CGColorRef fromColor = [UIColor blueColor].CGColor;
// 设置渐变的中间颜色为红色
CGColorRef midColor = [UIColor redColor].CGColor;
// 设置渐变的结束颜色为黄色
CGColorRef toColor =[UIColor yellowColor].CGColor;
// 创建一个数组对象，存储三个颜色变量
NSArray *colorArray = [NSArray arrayWithObjects:(__bridge id)(fromColor),midColor,toColor, nil];
// 设置 【渐变层的颜色数组属性】 为 【三个渐变色构建的数组】
gradientLayer.colors = colorArray;

// 将渐变层添加到视图对象的层中
[view.layer addSublayer:gradientLayer];

[self.view addSubview:view];
```

###  8. UIView视图的纹理填充

将图片赋予颜色对象作为背景：

```objective-c
UIImage *image = [UIImage imageNamed:@"red-1108405__340"];
// 新建一个颜色对象，并将导入的图片赋予该对象
UIColor *background = [[UIColor alloc] initWithPatternImage:image];
//再将此颜色对象，赋值给当前窗口根视图的背景。
self.view.backgroundColor = background;
```

### 9. CGAffineTransform仿射变换

```objective-c
// 使用仿射变换功能，旋转视图
CGRect myImageRect = CGRectMake(50.0f, 150.0f, 200.0f, 50.0f);
UIView *myView = [[UIView alloc] initWithFrame:myImageRect];
myView.backgroundColor = [UIColor redColor];
[self.view addSubview:myView];

//创建一个仿射变换变量，仿射变换可以用于平移、旋转、缩放变换路径或者图形上下文。
//Rotate
CGAffineTransform transform = myView.transform;
transform = CGAffineTransformRotate(transform, 3.14/4);
myView.transform = transform;
```

### 10. UITapGestureRecognizer手势之单击

```objective-c
- (void)viewDidLoad {
    [super viewDidLoad];

    CGRect rect = CGRectMake(100, 100, 107, 107);
    UIImageView *imageView = [[UIImageView alloc] initWithFrame:rect];
    
    UIImage *img = [UIImage imageNamed:@"lion"];
    imageView.image = img;
    
    // 开启图像视图的交互功能,default is NO
    [imageView setUserInteractionEnabled:YES];
    [self.view addSubview:imageView];
    
    // 手势识别类，用于检测发生在设备中的各种手势
    UITapGestureRecognizer *tapGuesture = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(singleTap)];
    // 将手势指定给图像视图
    [imageView addGestureRecognizer:tapGuesture];
}

- (void)singleTap {
    UIAlertView *alertView = [[UIAlertView alloc] initWithTitle:@"Information" message:@"single Tap" delegate:self cancelButtonTitle:@"OK" otherButtonTitles:nil, nil];
    [alertView show];
}
```



### 11. UITapGestureRecognizer手势之长按

```objective-c
- (void)viewDidLoad {
    [super viewDidLoad];
    
    CGRect rect = CGRectMake(100, 100, 107, 107);
    UIImageView *imageView = [[UIImageView alloc] initWithFrame:rect];
    
    UIImage *img = [UIImage imageNamed:@"lion"];
    imageView.image = img;
    
    // 开启图像视图的交互功能,default is NO
    [imageView setUserInteractionEnabled:YES];
    [self.view addSubview:imageView];
        
    // 创建一个长按手势识别对象
    UILongPressGestureRecognizer *longPressGuesture = [[UILongPressGestureRecognizer alloc] initWithTarget:self action:@selector(longPress:)];
    [imageView addGestureRecognizer:longPressGuesture];
}

- (void)longPress:(UILongPressGestureRecognizer *)guesture {
    // 检测手势事件的阶段
    if (guesture.state == UIGestureRecognizerStateBegan) {
        UIAlertView *alertView = [[UIAlertView alloc] initWithTitle:@"Information" message:@"long Tap" delegate:self cancelButtonTitle:@"OK" otherButtonTitles:nil, nil];
        [alertView show];
    }
}
```



### 12. UITapGestureRecognizer手势之双击

```objective-c
- (void)viewDidLoad {
    [super viewDidLoad];
    
    CGRect rect = CGRectMake(100, 100, 107, 107);
    UIImageView *imageView = [[UIImageView alloc] initWithFrame:rect];
    
    UIImage *img = [UIImage imageNamed:@"lion"];
    imageView.image = img;
    
    // 开启图像视图的交互功能,default is NO
    [imageView setUserInteractionEnabled:YES];
    [self.view addSubview:imageView];
    
    // 手势识别类，用于检测发生在设备中的各种手势
    UITapGestureRecognizer *tapGuesture = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(doubleTap)];
    
    // 设置点击次数为2，模拟双击事件。Default is 1. <-需要匹配的点击次数
    [tapGuesture setNumberOfTapsRequired:2];
    //  设置为单次双击事件。Default is 1.<-需要匹配的手指数
    [tapGuesture setNumberOfTouchesRequired:1];
    
    // 将手势指定给图像视图
    [imageView addGestureRecognizer:tapGuesture];
}    

- (void)doubleTap {
    UIAlertView *alertView = [[UIAlertView alloc] initWithTitle:@"Information" message:@"double Tap" delegate:self cancelButtonTitle:@"OK" otherButtonTitles:nil, nil];
    [alertView show];

}
```

### 参考
* [**Demo:触摸事件与UIResponder**](http://www.jianshu.com/p/69ebc4161815)
* [iOS-UIGestureRecognizer 详解 - 原理篇](https://www.jianshu.com/p/4ca805af1570)
