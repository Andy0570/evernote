### 步骤进度条效果参考

![](http://upload-images.jianshu.io/upload_images/2648731-fb742463e332f4b3..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/800)



iOS UIKit 框架中并没有提供类似的控件，我们可以使用 UIProgressView、UIView、UILabel 组合实现步骤进度条效果。

* UIProgressView——实现水平的进度条效果；
* UIView——把UIView裁剪成圆形，实现索引节点效果；
* UILabel——每个节点下面的提示文字。



### 源码

将步骤进度条封装成一个 HQLStepView 类，它是 UIView 的子类。

#### HQLStepView.h 文件

```objectivec
#import <UIKit/UIKit.h>

@interface HQLStepView : UIView

// 指定初始化方法
- (instancetype)initWithFrame:(CGRect)frame titlesArray:(NSArray *)titlesArray stepIndex:(NSUInteger)stepIndex;

// 设置当前步骤
- (void)setStepIndex:(NSUInteger)stepIndex animation:(BOOL)animation;

@end
```

#### HQLStepView.m 文件

```objectivec
#import "HQLStepView.h"

// 步骤条主题色
#define TINT_COLOR [UIColor colorWithRed:35/255.f green:135/255.f blue:255/255.f alpha:1]

@interface HQLStepView ()

@property (nonatomic, copy) NSArray *titlesArray;
@property (nonatomic, assign) NSUInteger stepIndex;

@property (nonatomic, strong) UIProgressView *progressView;
@property (nonatomic, strong) NSMutableArray *circleViewArray;
@property (nonatomic, strong) NSMutableArray *titleLabelArray;
@property (nonatomic, strong) UILabel *indicatorLabel;

@end

@implementation HQLStepView

#pragma mark - Init

- (instancetype)initWithFrame:(CGRect)frame titlesArray:(NSArray *)titlesArray stepIndex:(NSUInteger)stepIndex {
    self = [super initWithFrame:frame];
    if (self) {
        _titlesArray = [titlesArray copy];
        _stepIndex = stepIndex;

        // 进度条
        [self addSubview:self.progressView];
        
        for (NSString *title in _titlesArray) {
            
            // 圆圈
            UIView *circle = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 13, 13)];
            circle.backgroundColor = [UIColor lightGrayColor];
            circle.layer.cornerRadius = 13.0f / 2;
            [self addSubview:circle];
            [self.circleViewArray addObject:circle];
            
            // 标题
            UILabel *label = [[UILabel alloc] init];
            label.text = title;
            label.font = [UIFont systemFontOfSize:14];
            label.textAlignment = NSTextAlignmentCenter;
            [self addSubview:label];
            [self.titleLabelArray addObject:label];
        }
        
        // 当前索引数字
        [self addSubview:self.indicatorLabel];
    }
    return self;
}

// 布局更新页面元素
- (void)layoutSubviews {
    NSInteger perWidth = self.frame.size.width / self.titlesArray.count;
    
    // 进度条
    self.progressView.frame = CGRectMake(0, 0, self.frame.size.width - perWidth, 1);
    self.progressView.center = CGPointMake(self.frame.size.width / 2, self.frame.size.height / 4);
    
    CGFloat startX = self.progressView.frame.origin.x;
    for (int i = 0; i < self.titlesArray.count; i++) {
        // 圆圈
        UIView *cycle = self.circleViewArray[i];
        if (cycle) {
            cycle.center = CGPointMake(i * perWidth + startX, self.progressView.center.y);
        }
        
        // 标题
        UILabel *label = self.titleLabelArray[i];
        if (label) {
            label.frame = CGRectMake(perWidth * i, self.frame.size.height / 2, self.frame.size.width / self.titlesArray.count, self.frame.size.height / 2 );
        }
    }
    self.stepIndex = self.stepIndex;
}

#pragma mark - Custom Accessors

- (UIProgressView *)progressView {
    if (!_progressView) {
        _progressView = [[UIProgressView alloc] initWithProgressViewStyle:UIProgressViewStyleDefault];
        _progressView.progressTintColor = TINT_COLOR;
        _progressView.progress = self.stepIndex / ((self.titlesArray.count - 1) * 1.0);
    }
    return _progressView;
}

- (UILabel *)indicatorLabel {
    if (!_indicatorLabel) {
        _indicatorLabel = [[UILabel alloc] initWithFrame:CGRectMake(0, 0, 23, 23)];
        _indicatorLabel.textColor = TINT_COLOR;
        _indicatorLabel.textAlignment = NSTextAlignmentCenter;
        _indicatorLabel.backgroundColor = [UIColor whiteColor];
        _indicatorLabel.layer.cornerRadius = 23.0f / 2;
        _indicatorLabel.layer.borderColor = [TINT_COLOR CGColor];
        _indicatorLabel.layer.borderWidth = 1;
        _indicatorLabel.layer.masksToBounds = YES;
    }
    return _indicatorLabel;
}

- (NSMutableArray *)circleViewArray {
    if (!_circleViewArray) {
        _circleViewArray = [[NSMutableArray alloc] initWithCapacity:self.titlesArray.count];
    }
    return _circleViewArray;
}

- (NSMutableArray *)titleLabelArray {
    if (!_titleLabelArray) {
        _titleLabelArray = [[NSMutableArray alloc] initWithCapacity:self.titlesArray.count];
    }
    return _titleLabelArray;
}

// 设置当前进度索引，更新圆形图片、文本颜色、当前索引数字
- (void)setStepIndex:(NSUInteger)stepIndex {
    for (int i = 0; i < self.titlesArray.count; i++) {
        UIView *cycle = self.circleViewArray[i];
        UILabel *label = self.titleLabelArray[i];
        if (stepIndex >= i) {
            cycle.backgroundColor = TINT_COLOR;
            label.textColor = TINT_COLOR;
        } else {
            cycle.backgroundColor = [UIColor lightGrayColor];
            label.textColor = [UIColor lightGrayColor];
        }
    }
}

#pragma mark - Public

- (void)setStepIndex:(NSUInteger)stepIndex animation:(BOOL)animation {
    if (stepIndex < self.titlesArray.count) {
        // 更新颜色
        self.stepIndex = stepIndex;
        // 设置进度条
        [self.progressView setProgress:stepIndex / ((self.titlesArray.count - 1) * 1.0) animated:animation];
        // 设置当前索引数字
        self.indicatorLabel.text = [NSString stringWithFormat:@"%lu", stepIndex + 1];
        self.indicatorLabel.center = ((UIView *)[self.circleViewArray objectAtIndex:stepIndex]).center;
    }
}

@end

```

### 接口调用：

```objectivec
- (void)viewDidLoad {
    [super viewDidLoad];
    
    // 初始化
    _hqlStepView = [[HQLStepView alloc] initWithFrame:CGRectMake(0, 200, self.view.frame.size.width, 60) titlesArray:@[@"第一步", @"第二步", @"第三步"] stepIndex:0];
    [self.view addSubview:_hqlStepView];
}

- (void)viewDidAppear:(BOOL)animated {
    [super viewDidAppear:animated];
    
    // 设置当前步骤，步骤索引=数组索引
    [_hqlStepView setStepIndex:0 animation:YES];
}
```

效果：



![](http://upload-images.jianshu.io/upload_images/2648731-c30e9403eccae140..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)



因为 **UIProgressView** 实现的水平进度条高度值默认为1，设置frame是无效的。可以通过仿射变换的方式增加它的高度。

### 第三方框架
* [GitHub: ISTimeline](https://github.com/instant-solutions/ISTimeline) ⭐️900
* [GitHub: TimelineTableViewCell](https://github.com/kf99916/TimelineTableViewCell) ⭐️800

### 参考：

* [iOS 自定义步骤进度条](http://www.cnblogs.com/5ishare/p/5044447.html?utm_source=tuicool&utm_medium=referral)
* [XFStepProgress](https://github.com/levenwhf/XFStepProgress)
