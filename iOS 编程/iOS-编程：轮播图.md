### SDCycleScrollView
傻瓜式最简单的方法，调用第三方框架，[SDCycleScrollView](https://github.com/gsdios/SDCycleScrollView)

### 下面👇的自定义方法，模仿着写的，待完善。
参考的[无限轮播(三图轮播原理)](http://www.jianshu.com/p/2b30ad110ba7),自己模仿写了一个，但是发现图片一开始加载的时候有偏移量，一直没法解决，先留着吧。


```
#import <UIKit/UIKit.h>

/**
 首页轮播图
 */
@interface HQLCarouselView : UIView

#pragma 视图控制器中调用的接口
- (instancetype) initWithFrame:(CGRect)frame withPictures:(NSArray *)picture_array;

@end
```

---

```
#import "HQLCarouselView.h"

#define KWIDTH self.bounds.size.width
#define KHEIGHT self.bounds.size.height

@interface HQLCarouselView () <UIScrollViewDelegate>

/** 存放图片名称的数组*/
@property (nonatomic,strong) NSArray *picture_array;
/** 记录图片数组的下标*/
@property (nonatomic,assign) NSInteger index;
/** 滚动视图*/
@property (nonatomic,strong) UIScrollView *scrollView;
/** 页面控件*/
@property (nonatomic,strong) UIPageControl *pageControl;
/** 计时器*/
@property (nonatomic,strong) NSTimer *timer;

@end

@implementation HQLCarouselView

#pragma 初始化
- (instancetype) initWithFrame:(CGRect)frame withPictures:(NSArray *)picture_array {
    
    self = [super initWithFrame:frame];
    if (self) {
        self.picture_array = picture_array;
        self.index = 0;
        [self addSubview:self.scrollView];
        [self addImageViewToScrollView];
        [self addSubview:self.pageControl];
        [self initTimer];
    }
    return self;
    
}

#pragma 延迟加载
- (NSArray *)picture_array {
    if (!_picture_array) {
        _picture_array = [[NSArray alloc] init];
    }
    return _picture_array;
}

- (UIScrollView *)scrollView {
    if (!_scrollView) {
        _scrollView = [[UIScrollView alloc] initWithFrame:self.frame];
        _scrollView.delegate =self;
        _scrollView.showsHorizontalScrollIndicator = NO;    //隐藏滚动条
        _scrollView.pagingEnabled = YES;
        _scrollView.contentSize = CGSizeMake(KWIDTH*3, 0);  //设置可滑尺寸
        _scrollView.contentOffset = CGPointMake(0, 0);    //设置初始偏移量
    }
    return _scrollView;
}

- (UIPageControl *)pageControl {
    if (!_pageControl) {
        _pageControl = [[UIPageControl alloc] initWithFrame:CGRectMake(KWIDTH / 2.0 - 50, KHEIGHT-5, 100, 50)]; //圆点位置
        _pageControl.numberOfPages = self.picture_array.count;  //  圆点个数
        _pageControl.currentPage = 0;   //初始选中第一个圆点
        _pageControl.pageIndicatorTintColor = [UIColor whiteColor]; //圆点颜色
        _pageControl.currentPageIndicatorTintColor = [UIColor greenColor];  //  当前圆点颜色
        _pageControl.enabled = NO;  //由于后面要添加计时器，所以此处取消圆点选中事件
    }
    return _pageControl;
}

#pragma 添加image
//往scrollView上添加imgView:初始时让第二个imgView显示第一张图片(三图轮播,因此只创建三个imgView即可,始终让中间的imgView显示当前图片)
- (void)addImageViewToScrollView {
    for (int i = 0; i < 3; i++) {
        UIImageView *imgView = [[UIImageView alloc] initWithFrame:CGRectMake(KWIDTH*i, 0, KWIDTH, KHEIGHT)];
        imgView.tag = 1000 + i; //添加标记，方便后面找到
        if (i == 0) {
            imgView.image = [UIImage imageNamed:self.picture_array.firstObject];
        }else if (i ==1){
            imgView.image = [UIImage imageNamed:self.picture_array[1]];
        }else {
            imgView.image = [UIImage imageNamed:self.picture_array.lastObject];
        }
        
        imgView.contentMode =UIViewContentModeScaleToFill;
        [self.scrollView addSubview:imgView];
    }
}

// scrollView结束减速时执行
- (void)scrollViewDidEndDecelerating:(UIScrollView *)scrollView {
    if (scrollView.contentOffset.x >= KWIDTH) {
        //根据偏移量向左还是向右分别控制index的值
        if (self.index == self.picture_array.count -1) {
            self.index = 0;
        }else {
            self.index ++;
        }
    }
    //调整好index值之后重新设置下偏移量以及当前选中的圆点
    [self.scrollView setContentOffset:CGPointMake(KWIDTH, 0) animated:NO];
    self.pageControl.currentPage = self.index;
    
    //让中间的imgView始终显示index位置的图片（中心思想）
    [self addImage:self.index];
}

//改变ImgView显示的图片名称
- (void)addImage:(NSInteger )index {
    
    //找到添加到scrollView上的imgView
    UIImageView *imageView1 = (UIImageView *)[self.scrollView viewWithTag:1000];
    UIImageView *imageView2 = (UIImageView *)[self.scrollView viewWithTag:1001];
    UIImageView *imageView3 = (UIImageView *)[self.scrollView viewWithTag:1002];
    if (index ==self.picture_array.count - 1) {
        imageView1.image = [UIImage imageNamed:self.picture_array[index - 1]];
        imageView2.image = [UIImage imageNamed:self.picture_array[index]];
        imageView3.image = [UIImage imageNamed:self.picture_array[0]];
    }
    else if (index ==0){
        imageView1.image = [UIImage imageNamed:self.picture_array.lastObject];
        imageView2.image = [UIImage imageNamed:self.picture_array[index]];
        imageView3.image = [UIImage imageNamed:self.picture_array[index + 1]];
    }
    else{
        imageView1.image = [UIImage imageNamed:self.picture_array[index - 1]];
        imageView2.image = [UIImage imageNamed:self.picture_array[index]];
        imageView3.image = [UIImage imageNamed:self.picture_array[index + 1]];
    }
}

//创建计时器
- (void)initTimer {
    self.timer = [NSTimer scheduledTimerWithTimeInterval:2.5
                                                  target:self
                                                selector:@selector(loadsScrollViewImage)
                                                userInfo:nil
                                                 repeats:YES];
}

//计时器要执行的方法：每次执行改变偏移量
- (void)loadsScrollViewImage {
    [self.scrollView setContentOffset:CGPointMake(self.scrollView.contentOffset.x + KWIDTH, 0) animated:YES];
}

//偏移量改变并且有滚动动画才会执行该方法，内部代码与上面结束减速（scrollViewDidEndDecelerating）要执行代码相同
- (void)scrollViewDidEndScrollingAnimation:(UIScrollView *)scrollView {

    if (scrollView.contentOffset.x >= KWIDTH) {
        //根据偏移量是向左还是向右分别控制index的值
        if (self.index == self.picture_array.count - 1) {
            self.index = 0;
        }else {
            self.index ++;
        }
    }else {
        if (self.index == 0) {
            self.index = self.picture_array.count -1;
        }else {
            self.index --;
        }
    }
    //调整好index的值之后重新设置下偏移量以及当前选中的
    [self.scrollView setContentOffset:CGPointMake(KWIDTH, 0) animated:NO];
    self.pageControl.currentPage = self.index;
    //让中间的imgView始终显示index位置的图片（中心思想）
    [self addImage:self.index];
}

//防止计时器与拖动手势冲突
- (void)scrollViewWillBeginDragging:(UIScrollView *)scrollView {
    [self.timer invalidate];
    self.timer = nil;
}

//拖动结束时开启一个新的计时器
- (void)scrollViewDidEndDragging:(UIScrollView *)scrollView willDecelerate:(BOOL)decelerate {
    [self initTimer];
}

@end

```
主视图控制器中调用：

```
//轮播图
_carouselView = [[HQLCarouselView alloc] initWithFrame:CGRectMake(0,32,self.view.width,self.view.width * self.view.height / 1440) withPictures:@[@"slide_000.png",@"slide_001.png",@"slide_002.png"]];
[self.view addSubview:_carouselView];
```

### 参考文章
* [你还在用轮播图吗?](https://isux.tencent.com/carousels.html)
* [简单实现下拉图片放大③ - 定时器轮播图](http://www.jianshu.com/p/d9fa442e48f9)
* [轮播图:无限轮播(三图轮播原理)](http://www.jianshu.com/p/2b30ad110ba7)
