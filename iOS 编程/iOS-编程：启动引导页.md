## 启动引导页 

> 最近急着用启动引导图，折腾磨蹭了俩礼拜，所以搜了很多资料，最后终于用[SRNewFeatures](https://github.com/guowilling/SRNewFeatures)实现了。

使用当中发现发现如果使用**Assets.xcassets**中的Image是无法显示的，只有把图片放在**根目录**里才能显示。
开始以为是图片加载的问题：参考：[UIImage加载图片的方式以及Images.xcassets对于加载方法的影响](http://www.jianshu.com/p/5358f587af38)
后来研究了一下发现是在**Assets.xcassets**添加Image包的创建方式用错了，不应该使用**New iOS Launch Image** 的方式，而是使用**New Image Set*** 的方式。



### 设计思想

#### 示例一：

```objectivec
// 获取上次启动应用保存的appVersion
NSString *lastVersion = [[NSUserDefaults standardUserDefaults] stringForKey:@"CFBundleShortVersionString"];
// 获取当前版本号
NSString *currentVersion = [NSBundle mainBundle].infoDictionary[@"CFBundleShortVersionString"];
if ([currentVersion isEqualToString:lastVersion]) {
    // 两个版本号相同，不是首次登陆
    return NO;
} else {
    // 否则存入当前版本信息
    [[NSUserDefaults standardUserDefaults] setObject:currentVersion forKey:@"CFBundleShortVersionString"];
    [[NSUserDefaults standardUserDefaults] synchronize];
    return YES;
}
```

#### 示例二：

```objectivec
static NSString *const kAppVersion = @"appVersion";

// ...

#pragma mark - 判断是不是首次登录或者版本更新
-(BOOL )isFirstLauch {
    // 获取当前版本号
    NSString *currentVersion = [NSBundle mainBundle].infoDictionary[@"CFBundleShortVersionString"];
    // 获取上次启动应用保存的Version
    NSString *lastVersion = [[NSUserDefaults standardUserDefaults] objectForKey:kAppVersion];
    // 版本升级(即两次版本号不相同)或首次登录(即获取到上次版本号为nil)
    BOOL isEqualVerson = [lastVersion isEqualToString:currentVersion];
    if (!lastVersion || !isEqualVerson) {
        [[NSUserDefaults standardUserDefaults] setObject:currentVersion forKey:kAppVersion];
        [[NSUserDefaults standardUserDefaults] synchronize];
        return YES;
    }else{
        return NO;
    }
}
```

参考 Demo：
```objectivec
//
//  TPPWelcomeView.m
//  TaoPiaoPiao
//
//  Created by blackcater on 16/7/5.
//  Copyright (c) 2016 blackcater. All rights reserved.
//

#import "TPPWelcomeView.h"

@interface TPPWelcomeView() <UIScrollViewDelegate>

@property (nonatomic, strong) UIScrollView *scrollView;
@property (nonatomic, strong) UIPageControl *pageControl;

@end

@implementation TPPWelcomeView

#pragma mark - Lifecycle

- (instancetype)initWithFrame:(CGRect)frame {
    self = [super initWithFrame:frame];

    if (self) {
        [self render];
    }

    return self;
}

- (void)render {
    [self addImagesToScrollView];
    [self addSubview:self.scrollView];
    [self addSubview:self.pageControl];
}

#pragma mark - Custom Accessors

- (UIScrollView *)scrollView {
    if (!_scrollView) {
        _scrollView = [[UIScrollView alloc] initWithFrame:CGRectMake(0, 0, SCREEN_WIDTH, SCREEN_HEIGHT)];

        _scrollView.minimumZoomScale = 1.0;
        _scrollView.maximumZoomScale = 1.0;

        _scrollView.showsVerticalScrollIndicator = NO;
        _scrollView.showsHorizontalScrollIndicator = NO;
        
        // bounces,布尔值，用于控制当滚动视图超过内容边缘是否反弹，然后返回。
        _scrollView.bounces = NO;
        _scrollView.pagingEnabled = YES; // scrollView 以边界倍数滚动
        
        // scrollView 的宽度 = SCREEN_WIDTH * 图片数量
        _scrollView.contentSize = CGSizeMake(SCREEN_WIDTH*WELCOME_PAHE_NUM, SCREEN_HEIGHT);
        _scrollView.contentOffset = CGPointZero; // 初始位置

        _scrollView.delegate = self;
    }

    return _scrollView;
}

- (UIPageControl *)pageControl {
    if (!_pageControl) {
        _pageControl = [[UIPageControl alloc] initWithFrame:CGRectMake((SCREEN_WIDTH-300)/2, (SCREEN_HEIGHT-14-6), 300, 14)];
        _pageControl.numberOfPages = WELCOME_PAHE_NUM;
        _pageControl.currentPage = 0;

        _pageControl.currentPageIndicatorTintColor = COLOR_RGB(239, 26, 40);
        _pageControl.pageIndicatorTintColor = COLOR_RGB(245, 245, 245);
    }

    return _pageControl;
}

#pragma mark - Private

- (void)addImagesToScrollView {
    int num = WELCOME_PAHE_NUM;
    for (int i=0; i<num; i++) {
        // 循环遍历添加图片
        UIImageView *imageView = [[UIImageView alloc] initWithImage:[UIImage imageNamed:[NSString stringWithFormat:@"%@%d.jpg", WELCOME_FILE_NAME, (i+1)]]];
        imageView.frame = CGRectMake(SCREEN_WIDTH*i, 0, SCREEN_WIDTH, SCREEN_HEIGHT);
        [self.scrollView addSubview:imageView];
        
        // 📝 最后一页时添加按钮
        if ((i+1) == num) {
            // 添加button
            UIButton *button = [[UIButton alloc] initWithFrame:CGRectMake((SCREEN_WIDTH-200)/2, 460, 200, 44)];
            [button setTitle:@"立刻观影" forState:UIControlStateNormal];
            [button setTitleColor: COLOR_RGB(239, 26, 40) forState:UIControlStateNormal];
            button.layer.borderColor = COLOR_RGB(239, 26, 40).CGColor;
            button.layer.borderWidth = 2;
            button.layer.cornerRadius = 20;
            button.clipsToBounds = YES;
            button.backgroundColor = [UIColor clearColor];
            
            [button addTarget:self action:@selector(btnClickDownHandler:) forControlEvents:UIControlEventTouchDown];
            [button addTarget:self action:@selector(btnClickUpHandler:) forControlEvents:UIControlEventTouchUpInside];

            [imageView addSubview:button];
            imageView.userInteractionEnabled = YES;
        }
    }
}

#pragma mark - IBActions

/** 
 * 📌 Target-Action 事件：按钮按下时，高亮背景颜色
 *
 * 优化建议：
 * [UIImage imageWitColor:HexColor(@"#000000")]
 * [button setBackgroundImage: forState:UIControlStateHighlighted];
 */
- (void)btnClickDownHandler:(UIButton *)button {
    button.backgroundColor = COLOR_RGB(239, 26, 40);
    [button setTitleColor:[UIColor whiteColor] forState:UIControlStateHighlighted];
}

- (void)btnClickUpHandler:(UIButton *)button {
    button.backgroundColor = [UIColor clearColor];

    [UIView animateWithDuration:1.0 delay:0.0 options:UIViewAnimationOptionCurveEaseInOut animations:^{
        self.alpha = 0.0;
    } completion:^(BOOL flag) {
        // 清除视图
        [self removeFromSuperview];
    }];
}

#pragma mark -
#pragma mark UIScrollViewDelegate
- (void)scrollViewDidScroll:(UIScrollView *)scrollView {
    // 根据 scrollView 的偏移量计算出当前页索引，并更新 pageControl
    int index = (int)floor(scrollView.contentOffset.x/SCREEN_WIDTH);
    self.pageControl.currentPage = index;
}


@end
```

#### AppDelegate.m
```objectivec
    // 欢迎页
    NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
    BOOL flag = [[defaults objectForKey:WELCOME_ACTIVE_NAME] boolValue];
    if (!flag) {
        [defaults setBool:YES forKey:WELCOME_ACTIVE_NAME];
        // 设置启动页
        TPPWelcomeView *welcomeView = [[TPPWelcomeView alloc] initWithFrame:CGRectMake(0, 0, SCREEN_WIDTH, SCREEN_HEIGHT)];
        [self.window addSubview:welcomeView];
    }
```

### 参考

* [SRNewFeatures](https://github.com/guowilling/SRNewFeatures) 
* [一句代码搞定启动引导页——LaunchIntroductionView.m](http://www.jianshu.com/p/f08891ccb218)
* [0代码实现项目引导页——KSGuidViewController](http://www.code4app.com/thread-10485-1-1.html)


### 其他第三方框架

* [**EAIntroView**](https://github.com/ealeksandrov/EAIntroView) ⭐️ 3400+
* [**Onboard**](https://github.com/mamaral/Onboard)  ⭐️ 5600+



### 设计思想

* [令人难忘的引导页欣赏](http://www.woshipm.com/discuss/114689.html)
* [22个知名APP启动引导页设计欣赏](http://www.3lian.com/edu/2014/07-10/154957.html)
* [移动端引导设计技巧1：前置的引导页](http://isux.tencent.com/user-guide-design-01.html)
* [由引导页说开来 | 网易用户体验设计中心](http://uedc.163.com/12264.html)
* [腾讯实例：怎么样让引导页变得更有用？](http://wenku.baidu.com/link?url=xbOPxzSa7bkM57FNX0P7IuXBuEvgKYJzoEp1ICQWPAZnlu0O5JRMT0e25wWlagiCvq-TOrE6PwQH4XrJSLeBuPyraFtI8FyHl3U6fJGb3TK)
