* GitHub源码：[TXScrollLabelView](https://github.com/tingxins/TXScrollLabelView)
*  star: 400+

>                                 ⭐️⭐️⭐️ 
以下内容来源于官方源码、 README 文档、测试 Demo或个人使用总结 ！

### 用法

```
/** Step1: 滚动文字 */
NSString *scrollTitle = @"xxxxxx";

/** Step2: 创建 ScrollLabelView */
TXScrollLabelView *scrollLabelView = [TXScrollLabelView scrollWithTitle:scrollTitle type:TXScrollLabelViewTypeFlipNoRepeat velocity:velocity options:UIViewAnimationOptionCurveEaseInOut];

/** Step3: 设置代理进行回调(Optional) */
scrollLabelView.scrollLabelViewDelegate = self;

/** Step4: 布局(Required) */
scrollLabelView.frame = CGRectMake(50, 100, 300, 30);
[self.view addSubview:scrollLabelView];

/** Step5: 开始滚动(Start scrolling!) */
[scrollLabelView beginScrolling];
```

Demo:
```
    // 滚动Label
    // step1:获取滚动内容
    NSString *scrollTitle = @"恭喜 XXX 网上商城隆重上线！";
    // step2:创建scrollLabelView对象
    TXScrollLabelView *scrollLabelView = [TXScrollLabelView scrollWithTitle:scrollTitle type:TXScrollLabelViewTypeFlipRepeat velocity:3 options:UIViewAnimationOptionTransitionNone];
    scrollLabelView.backgroundColor = [UIColor clearColor];
    scrollLabelView.scrollTitleColor = [UIColor blackColor];
    // step3:设置代理进行回调（Optional）
//    scrollLabelView.scrollLabelViewDelegate = self;
    // step4:布局
    scrollLabelView.frame = CGRectMake(31, 31, 240, 21);
    [self.view addSubview:scrollLabelView];
//    [scrollLabelView mas_makeConstraints:^(MASConstraintMaker *make) {
//        make.top.equalTo(speakerImage.mas_top);
//        make.left.equalTo(speakerImage.mas_right).with.offset(0);
//        make.size.mas_equalTo(CGSizeMake(240, 21));
//    }];
    // step5:开始滚动
    [scrollLabelView beginScrolling];
```

### 其他
* [Wonderful](https://github.com/dsxNiubility/Wonderful)  
这个框架里也有跑马灯的两个类，提取出来使用对于整个APP来说相比于这个框架更轻量简介些。

* 使用：
```
CGRect rect = CGRectMake(31,31, 240, 21);
SXHeadLine *headline = [[SXHeadLine alloc] initWithFrame:rect];
headline.messageArray = @[@"恭喜 XXX 网上商城隆重上线！！"];
[headline setScrollDuration:0.5 stayDuration:3.0];
[headline changeTapMarqueeAction:^(NSInteger index) {
        // 处理点击事件
    }];
[headline start];
[self.view addSubview:headline];
```

* [SDCycleScrollView](https://github.com/gsdios/SDCycleScrollView)
 ⚠️最近又发现，无限轮播器的这个框架也有类似跑马灯的效果，它是在循环的时候不显示图片，只循环显示文字。

* 使用：
```
// >>>>>>>>>>>>>>>>>>>>>>>>> demo轮播图4 >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
    
    // 网络加载 --- 创建只上下滚动展示文字的轮播器
    // 由于模拟器的渲染问题，如果发现轮播时有一条线不必处理，模拟器放大到100%或者真机调试是不会出现那条线的
    SDCycleScrollView *cycleScrollView4 = [SDCycleScrollView
                                           cycleScrollViewWithFrame:CGRectMake(0, 750, w, 40)
                                           delegate:self
                                           placeholderImage:nil];
    cycleScrollView4.scrollDirection = UICollectionViewScrollDirectionVertical;
    cycleScrollView4.onlyDisplayText = YES;
    
    NSMutableArray *titlesArray = [NSMutableArray new];
    [titlesArray addObject:@"纯文字上下滚动轮播"];
    [titlesArray addObject:@"纯文字上下滚动轮播 -- demo轮播图4"];
    [titlesArray addObjectsFromArray:titles];
    cycleScrollView4.titlesGroup = [titlesArray copy];
    
    [demoContainerView addSubview:cycleScrollView4];
```


### 参考
* [iOS 跑马灯之 TXScrollLabelView](http://www.jianshu.com/p/8f1f1b1ee814)
