* GitHub: [MJRefresh](https://github.com/CoderMJLee/MJRefresh)
* star: 13.2k


## README 文档

当前版本：V3.4.1


## 支持的视图控制器类型

* `UIScrollView`、
* `UITableView`、
* `UICollectionView`、
* `UIWebView`



## MJRefresh 中类的层次结构图

![](https://upload-images.jianshu.io/upload_images/2648731-92807314cf343476.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/800)


- 图表中 **红色文字显示的类** 是你可以直接使用的类。
- 下拉刷新控件类型：
    - 标准类型：`MJRefreshNormalHeader`
    - Gif 类型：`MJRefreshGifHeader`
- 上拉刷新控件类型：
    - 自动刷新类型：
        - 标准类型：`MJRefreshAutoNormalFooter`
        - Gif 类型：`MJRefreshAutoGifFooter`
    - 自动回弹类型： 
        - 标准类型：`MJRefreshBackNormalFooter`
        - Gif 类型：`MJRefreshBackGifFooter`
- 图表中非红色文字的类：用于继承，通过 DIY 的方式控制刷新。
- 关于如何通过 DIY 的方式控制刷新，你可以参考以下的图表：

![](https://upload-images.jianshu.io/upload_images/2648731-80f3058552dd34f8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/800)



注：[MJRefresh](https://github.com/CoderMJLee/MJRefresh) 的源码有完整的中文注释，建议大家直接看源码！

## MJRefreshComponent.h

```objectivec
/** 刷新控制器的基类 */
@interface MJRefreshComponent : UIView

#pragma mark -  控制刷新状态

// 开始刷新
- (void)beginRefreshing;
// 结束刷新
- (void)endRefreshing; 
// 返回一个 BOOL 值，显示当前刷新控件是否正在刷新
- (BOOL)isRefreshing;

#pragma mark - 其他

// 设置刷新控件是否根据拖动比例自动更改 alpha 透明度值
@property (assign, nonatomic, getter=isAutomaticallyChangeAlpha) BOOL automaticallyChangeAlpha;

@end
```



## MJRefreshHeader.h

基础的下拉刷新控件

```objectivec
@interface MJRefreshHeader : MJRefreshComponent

/** 创建header */
+ (instancetype)headerWithRefreshingBlock:(MJRefreshComponentAction)refreshingBlock;
/** 创建header */
+ (instancetype)headerWithRefreshingTarget:(id)target refreshingAction:(SEL)action;

/** 这个key用来存储上一次下拉刷新成功的时间 */
@property (copy, nonatomic) NSString *lastUpdatedTimeKey;
/** 上一次下拉刷新成功的时间 */
@property (strong, nonatomic, readonly, nullable) NSDate *lastUpdatedTime;

/** 忽略多少scrollView的contentInset的top */
@property (assign, nonatomic) CGFloat ignoredScrollViewContentInsetTop;

/** 默认是关闭状态, 如果遇到 CollectionView 的动画异常问题可以尝试打开 */
@property (nonatomic) BOOL isCollectionViewAnimationBug;

@end
```



## MJRefreshFooter.h

基础的上拉刷新控件

```objectivec
@interface MJRefreshFooter : MJRefreshComponent

/** 创建footer */
+ (instancetype)footerWithRefreshingBlock:(MJRefreshComponentAction)refreshingBlock;
/** 创建footer */
+ (instancetype)footerWithRefreshingTarget:(id)target refreshingAction:(SEL)action;

/** 提示没有更多的数据 */
- (void)endRefreshingWithNoMoreData;
- (void)noticeNoMoreData MJRefreshDeprecated("使用endRefreshingWithNoMoreData");

/** 重置没有更多的数据（消除没有更多数据的状态） */
- (void)resetNoMoreData;

/** 忽略多少scrollView的contentInset的bottom */
@property (assign, nonatomic) CGFloat ignoredScrollViewContentInsetBottom;

/** 自动根据有无数据来显示和隐藏（有数据就显示，没有数据隐藏。默认是NO） */
@property (assign, nonatomic, getter=isAutomaticallyHidden) BOOL automaticallyHidden MJRefreshDeprecated("已废弃此属性，开发者请自行控制footer的显示和隐藏");

@end
```



## MJRefreshAutoFooter.h

```objectivec
@interface MJRefreshAutoFooter : MJRefreshFooter
  
/** 是否自动刷新(默认为YES) */
@property (assign, nonatomic, getter=isAutomaticallyRefresh) BOOL automaticallyRefresh;

/** 当底部控件出现多少时就自动刷新(默认为1.0，也就是底部控件完全出现时，才会自动刷新) */
@property (assign, nonatomic) CGFloat appearencePercentTriggerAutoRefresh MJRefreshDeprecated("请使用triggerAutomaticallyRefreshPercent属性");

/** 当底部控件出现多少时就自动刷新(默认为1.0，也就是底部控件完全出现时，才会自动刷新) */
@property (assign, nonatomic) CGFloat triggerAutomaticallyRefreshPercent;

/** 自动触发次数, 默认为 1, 仅在拖拽 ScrollView 时才生效,
 
 如果为 -1, 则为无限触发
 */
@property (nonatomic) NSInteger autoTriggerTimes;

@end
```



## 参考

```objectivec
* 由于该框架还有更多功能，因此请不要写具体的文字来描述其用法。
* 您可以直接参考示例 MJTableViewController，MJCollectionViewController，MJWebViewController，更加直观，快捷。
```
<img src="http://images0.cnblogs.com/blog2015/497279/201506/141345470048120.png" width="30%" height="30%">



## UITableView + 下拉刷新，默认样式

```objectivec
self.tableView.mj_header = [MJRefreshNormalHeader headerWithRefreshingBlock:^{
   // 设置回调（一旦进入刷新状态就会调用这个 refreshingBlock）
}];
或

// 设置回调（一旦进入刷新状态，就调用 target 的 action，也就是调用 self 的 loadNewData 方法）
self.tableView.mj_header = [MJRefreshNormalHeader headerWithRefreshingTarget:self refreshingAction:@selector(loadNewData)];

// 马上进入刷新状态
[self.tableView.mj_header beginRefreshing];

//...
// 获取到数据后刷新列表
[self.tableView reloadData];

// 拿到当前的下拉刷新控件，结束刷新状态
[self.tableView.mj_header endRefreshing];
```
![(下拉刷新01-普通)](https://upload-images.jianshu.io/upload_images/2648731-4bb8f2168360bb91.gif?imageMogr2/auto-orient/strip)

## UITableView + 下拉刷新，动画图片

下拉刷新时，显示一个吃包子动画。

```objectivec
// 1. 设置回调（一旦进入刷新状态，就调用 target 的 action，也就是调用 self 的 loadNewData 方法）
MJRefreshGifHeader *header = [MJRefreshGifHeader headerWithRefreshingTarget:self refreshingAction:@selector(loadNewData)];

// 2. 设置即将刷新状态的动画图片（一松开就会刷新的状态）
NSMutableArray *idleImages = [NSMutableArray array];
for (NSUInteger i = 1; i<=60; i++) {
    UIImage *image = [UIImage imageNamed:[NSString stringWithFormat:@"dropdown_anim__000%zd", i]];
    [idleImages addObject:image];
}
[header setImages:idleImages forState:MJRefreshStateIdle];

// 3. 设置即将刷新状态的动画图片（一松开就会刷新的状态）
NSMutableArray *refreshingImages = [NSMutableArray array];
for (NSUInteger i = 1; i<=3; i++) {
    UIImage *image = [UIImage imageNamed:[NSString stringWithFormat:@"dropdown_loading_0%zd", i]];
    [refreshingImages addObject:image];
}
[header setImages:refreshingImages forState:MJRefreshStatePulling];

// 4. 设置正在刷新状态的动画图片
[header setImages:refreshingImages forState:MJRefreshStateRefreshing];

// 5. 将刷新控件添加到列表
self.tableView.mj_header = header;
```
💡 为了方便使用，建议将自定义动画的刷新控件继承 `MJRefreshGifHeader` 设置为子类对象，方便调用，实现起来也很简单，具体可以参考源码示例。





![(下拉刷新02-动画图片)](https://upload-images.jianshu.io/upload_images/2648731-5b1bb389c32dbfc1.gif?imageMogr2/auto-orient/strip)

## UITableView + 下拉刷新，隐藏时间
```objectivec
//...

// 设置自动切换透明度(在导航栏下面自动隐藏)
header.automaticallyChangeAlpha = YES;

// 隐藏时间
header.lastUpdatedTimeLabel.hidden = YES;

//...
```
![(下拉刷新03-隐藏时间)](https://upload-images.jianshu.io/upload_images/2648731-0a46328c629f41ff.gif?imageMogr2/auto-orient/strip)

## UITableView + 下拉刷新，隐藏刷新状态和时间
```objectivec
// 隐藏时间
header.lastUpdatedTimeLabel.hidden = YES;

// 隐藏状态
header.stateLabel.hidden = YES;
```
![(下拉刷新04-隐藏状态和时间0)](https://upload-images.jianshu.io/upload_images/2648731-83b093e15a4ec6e2.gif?imageMogr2/auto-orient/strip)

## UITableView + 下拉刷新，自定义刷新描述文字
```objectivec
// 设置回调（一旦进入刷新状态，就调用target的action，也就是调用self的loadNewData方法）
MJRefreshNormalHeader *header = [MJRefreshNormalHeader headerWithRefreshingTarget:self refreshingAction:@selector(loadNewData)];

// 设置文字
[header setTitle:@"Pull down to refresh" forState:MJRefreshStateIdle];
[header setTitle:@"Release to refresh" forState:MJRefreshStatePulling];
[header setTitle:@"Loading ..." forState:MJRefreshStateRefreshing];

// 设置字体
header.stateLabel.font = [UIFont systemFontOfSize:15];
header.lastUpdatedTimeLabel.font = [UIFont systemFontOfSize:14];

// 设置颜色
header.stateLabel.textColor = [UIColor redColor];
header.lastUpdatedTimeLabel.textColor = [UIColor blueColor];

// 马上进入刷新状态
[header beginRefreshing];

// 设置刷新控件
self.tableView.mj_header = header;
```
![(下拉刷新05-自定义文字)](https://upload-images.jianshu.io/upload_images/2648731-e41057bdfba667f3.gif?imageMogr2/auto-orient/strip)

## UITableView + 下拉刷新，自定义刷新控件
```objectivec
self.tableView.mj_header = [MJDIYHeader headerWithRefreshingTarget:self refreshingAction:@selector(loadNewData)];
// 具体实现参考 Demo 中的 MJDIYHeader.h 和 MJDIYHeader.m 文件
```
![(下拉刷新06-自定义刷新控件)](https://upload-images.jianshu.io/upload_images/2648731-af6d12b9b49c7812.gif?imageMogr2/auto-orient/strip)

## UITableView + 上拉刷新 默认
```objectivec
// 方法一：Block 回调方式
self.tableView.mj_footer = [MJRefreshAutoNormalFooter footerWithRefreshingBlock:^{
    //Call this Block When enter the refresh status automatically
}];

// 方法二：Target-Action 方式
// 设置回调（一旦进入刷新状态就会调用这个 refreshingBlock）
self.tableView.mj_footer = [MJRefreshAutoNormalFooter footerWithRefreshingTarget:self refreshingAction:@selector(loadMoreData)];
```
![(上拉刷新01-默认)](https://upload-images.jianshu.io/upload_images/2648731-db6ebd08c43b999e.gif?imageMogr2/auto-orient/strip)

## UITableView + 上拉刷新，动画图片
```objectivec
// 设置回调（一旦进入刷新状态，就调用target的action，也就是调用self的loadMoreData方法）
MJRefreshAutoGifFooter *footer = [MJRefreshAutoGifFooter footerWithRefreshingTarget:self refreshingAction:@selector(loadMoreData)];

// 设置刷新图片，传入一个包含图片的数组
[footer setImages:refreshingImages forState:MJRefreshStateRefreshing];

// Set footer
self.tableView.mj_footer = footer;
```
![(上拉刷新02-动画图片)](https://upload-images.jianshu.io/upload_images/2648731-31b8ba27f78fecf1.gif?imageMogr2/auto-orient/strip)

## UITableView + 上拉刷新，隐藏刷新状态的文字
```objectivec
// 当上拉刷新控件出现50%时（出现一半），就会自动刷新。这个值默认是1.0（也就是上拉刷新100%出现时，才会自动刷新）
footer.triggerAutomaticallyRefreshPercent = 0.5;

// 隐藏刷新状态的文字
footer.refreshingTitleHidden = YES;
// If does have not above method，then use footer.stateLabel.hidden = YES;
```
![(上拉刷新03-隐藏刷新状态的文字)](https://upload-images.jianshu.io/upload_images/2648731-48bf7a2ab8b21e65.gif?imageMogr2/auto-orient/strip)

## UITableView + 上拉刷新，刷新完成后提示全部加载完毕
```objectivec
// 拿到当前的上拉刷新控件，变为没有更多数据的状态
[footer noticeNoMoreData];

// 禁止自动加载
footer.automaticallyRefresh = NO;
```
![(上拉刷新04-全部加载完毕)](https://upload-images.jianshu.io/upload_images/2648731-bb07d7a1d849b00a.gif?imageMogr2/auto-orient/strip)

## UITableView + 上拉刷新，自定义文字
```objectivec
// 添加默认的上拉刷新
// 设置回调（一旦进入刷新状态，就调用target的action，也就是调用self的loadMoreData方法）
MJRefreshAutoNormalFooter *footer = [MJRefreshAutoNormalFooter footerWithRefreshingTarget:self refreshingAction:@selector(loadMoreData)];

// 设置文字
[footer setTitle:@"Click or drag up to refresh" forState:MJRefreshStateIdle];
[footer setTitle:@"Loading more ..." forState:MJRefreshStateRefreshing];
[footer setTitle:@"No more data" forState:MJRefreshStateNoMoreData];

// 设置字体
footer.stateLabel.font = [UIFont systemFontOfSize:17];

// 设置颜色
footer.stateLabel.textColor = [UIColor blueColor];

// 设置footer
self.tableView.mj_footer = footer;
```
![(上拉刷新05-自定义文字)](https://upload-images.jianshu.io/upload_images/2648731-f44ee2bc1ec17211.gif?imageMogr2/auto-orient/strip)

## UITableView + 上拉刷新，加载后隐藏
```objectivec
// Hidden current control of the pull to refresh
self.tableView.mj_footer.hidden = YES;
```
![(上拉刷新06-加载后隐藏)](https://upload-images.jianshu.io/upload_images/2648731-bcb7730d1164dce2.gif?imageMogr2/auto-orient/strip)

## UITableView + 上拉刷新，自动回弹的上拉01
```objectivec
// 设置回调（一旦进入刷新状态，就调用target的action，也就是调用self的loadMoreData方法）
self.tableView.mj_footer = [MJRefreshBackNormalFooter footerWithRefreshingTarget:self refreshingAction:@selector(loadMoreData)];
// 设置了底部inset
self.tableView.contentInset = UIEdgeInsetsMake(0, 0, 30, 0);
// 忽略掉底部inset
self.tableView.mj_footer.ignoredScrollViewContentInsetBottom = 30;
```
![(上拉刷新07-自动回弹的上拉01)](https://upload-images.jianshu.io/upload_images/2648731-cf7b7d60fa102f33.gif?imageMogr2/auto-orient/strip)

## UITableView + 上拉刷新，自定义刷新控件
```objectivec
MJRefreshBackGifFooter *footer = [MJRefreshBackGifFooter footerWithRefreshingTarget:self refreshingAction:@selector(loadMoreData)];

// Set the normal state of the animated image
[footer setImages:idleImages forState:MJRefreshStateIdle];
//  Set the pulling state of animated images（Enter the status of refreshing as soon as loosen）
[footer setImages:pullingImages forState:MJRefreshStatePulling];
// Set the refreshing state of animated images
[footer setImages:refreshingImages forState:MJRefreshStateRefreshing];

// Set footer
self.tableView.mj_footer = footer;
```
![(上拉刷新07-自动回弹的上拉02)](https://upload-images.jianshu.io/upload_images/2648731-93c138eac8058455.gif?imageMogr2/auto-orient/strip)

## UITableView + 上拉刷新 自定义刷新控件(自动刷新)
```objectivec
self.tableView.mj_footer = [MJDIYAutoFooter footerWithRefreshingTarget:self refreshingAction:@selector(loadMoreData)];
// Implementation reference to MJDIYAutoFooter.h和MJDIYAutoFooter.m
```
![(上拉刷新09-自定义刷新控件(自动刷新))](https://upload-images.jianshu.io/upload_images/2648731-fa64aad6b25fa7af.gif?imageMogr2/auto-orient/strip)

## UITableView + 上拉刷新，自定义刷新控件(自动回弹)
```objectivec
self.tableView.mj_footer = [MJDIYBackFooter footerWithRefreshingTarget:self refreshingAction:@selector(loadMoreData)];
// Implementation reference to MJDIYBackFooter.h和MJDIYBackFooter.m
```
![(上拉刷新10-自定义刷新控件(自动回弹))](https://upload-images.jianshu.io/upload_images/2648731-14a494218dcff7f3.gif?imageMogr2/auto-orient/strip)

## UICollectionView 上下拉刷新
```objectivec
// 下拉刷新
self.collectionView.mj_header = [MJRefreshNormalHeader headerWithRefreshingBlock:^{
   //Call this Block When enter the refresh status automatically 
   
  //...
   [weakSelf.collectionView reloadData];
   // 结束刷新
   [weakSelf.collectionView.mj_header endRefreshing];
}];


// 上拉刷新
self.collectionView.mj_footer = [MJRefreshAutoNormalFooter footerWithRefreshingBlock:^{
   //Call this Block When enter the refresh status automatically
   
   // ...
   [weakSelf.collectionView reloadData];
   // 结束刷新
   [weakSelf.collectionView.mj_footer endRefreshing];
}];

// 默认先隐藏footer
self.collectionView.mj_footer.hidden = YES;
```
![(UICollectionView01-上下拉刷新)](https://upload-images.jianshu.io/upload_images/2648731-f87faea625678044.gif?imageMogr2/auto-orient/strip)

## UIWebView01-下拉刷新

💡💡💡App Store 上新上架的 APP 自 20200401 起已经不支持 `UIWebView `了，请替换为 `WKWebView`。

```objectivec
// 添加下拉刷新控件
self.webView.scrollView.mj_header = [MJRefreshNormalHeader headerWithRefreshingBlock:^{
   //Call this Block When enter the refresh status automatically
}];
```
![(UICollectionView01-上下拉刷新)](https://upload-images.jianshu.io/upload_images/2648731-9158229181518a12.gif?imageMogr2/auto-orient/strip)




