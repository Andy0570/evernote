`UIScrollView` 滚动视图为展示内容比应用程序窗口大的视图提供支持。它允许用户通过触控手势移动内容并且通过捏合手势放大缩小内容。

### `UIScrollViewIndicatorStyle`

`UIScrollViewIndicatorStyle` 指示器样式，指的是类似于 web 页面上的边缘滚动条样式。

```objectivec
// 设置指示器（滚动条）样式，白色
self.scrollerView.indicatorStyle = UIScrollViewIndicatorStyleWhite;
// 加载时闪一下指示器（滚动条）
[self.scrollerView flashScrollIndicators];
```

### 在 `UIScrollView` 中放一张 2 倍于窗口大小的视图

视图层次结构：根视图控制器中添加 `UIScrollView` 滚动视图，在 `UIScrollView` 中添加自定义子视图 `HyponsisView`。

实现方法：覆盖 `UIViewController` 中的 `loadView` 方法，创建视图层次结构。
```objectivec
- (void)loadView {
//创建一个超大视图
CGRect screenRect = CGRectMake(0, 0, 414, 736);
//创建一个 UIScrollView 对象，将其尺寸设置为窗口大小
UIScrollView *scrollView = [[UIScrollView alloc] initWithFrame:screenRect];
self.view = scrollView;

// HyponsisView 的视图大小是窗口的2倍
CGRect bigRect = screenRect;
bigRect.size.width *= 2.0;
bigRect.size.height *= 2.0;
  
//创建一个有着超大尺寸的 HQLHypnosisView 对象并将其加入 UIScrollView 对象
HQLHypnosisView *hyponsisView = [[HQLHypnosisView alloc]initWithFrame:bigRect];

[scrollView addSubview:hyponsisView];

//告诉 UIScrollView 对象“取景”范围有多大
scrollView.contentSize = bigRect.size;
}
```

 

### 在 `UIScrollView` 中放左右两张视图


`UIScrollView` 滚动视图的**分页显示**实现原理：`UIScrollView` 对象会根据其 `bounds` 的尺寸，将 `contentSize` 分割为尺寸相同的多个区域。拖动结束后，`UIScrollView` 实例会自动滚动并只显示其中的一个区域。

同样覆盖 `loadView` 方法创建视图层次结构：

```objectivec
// 创建两个 CGRect 结构分别作为 `UIScrollView` 对象和 `HQLHypnosisView` 对象的 `frame`
CGRect screenRect = CGRectMake(0, 0, 414, 736);

// 创建一个 UIScrollView 对象，将其尺寸设置为窗口大小
UIScrollView *scrollView = [[UIScrollView alloc] initWithFrame:screenRect];

// 设置 UIScrollView 对象的“镜头”的边和其显示的某个视图的边对齐
[scrollView setPagingEnabled:YES];
self.view = scrollView;

CGRect bigRect = screenRect;
bigRect.size.width *= 2.0;

// 创建一个大小与屏幕相同的 HQLPictureView 对象并将其加入 UIScrollView 对象
HQLPictureView *pictureView = [[HQLPictureView alloc]initWithFrame:screenRect];
[scrollView addSubview:pictureView];

// 创建第二个大小与屏幕相同的 HQLHypnosisView 对象并放置在第一个 HQLPictureView 对象的右侧，使其刚好移除屏幕外
screenRect.origin.x +=screenRect.size.width;

HQLHypnosisView *anotherView = [[HQLHypnosisView alloc]initWithFrame:screenRect];

[scrollView addSubview:anotherView];

// 告诉 UIScrollView 对象“取景”范围有多大
scrollView.contentSize=bigRect.size;
```

### UIScrollViewDelegate 方法

```objectivec
// 该方法在 contentOffset 发生变化时调用
- (void)scrollViewDidScroll:(UIScrollView *)scrollView {
    // !!!: 判断手指滑动方向    
    CGPoint translatedPoint = [scrollView.panGestureRecognizer translationInView:scrollView];
    if (translatedPoint.y < 0) {
        NSLog(@"手指从下往上滑，浏览更多内容");
    } else if (translatedPoint.y > 0) {
        NSLog(@"手指从上往下滑，返回顶部");
    }
}

// 该方法在「将要开始拖拽时」调用
// 注：该方法可能需要先滑动一段时间（150ms）或距离才会被调用
- (void)scrollViewWillBeginDragging:(UIScrollView *)scrollView {
    NSLog(@"%s",__PRETTY_FUNCTION__);
}

// 该方法在「用户停止拖拽时」调用，以 velocity 为初速度，直到 targetContentOffset 停止。
// 应用程序可以通过修改 targetContentOffset 参数的值来调整停止的位置
- (void)scrollViewWillEndDragging:(UIScrollView *)scrollView withVelocity:(CGPoint)velocity targetContentOffset:(inout CGPoint *)targetContentOffset {
    NSLog(@"%s",__PRETTY_FUNCTION__);
}

// 该方法在「用户停止拖拽时」调用
// 如果在停止拖拽后继续移动，则 decelerate 参数为 YES，如果 decelerate（减速） 为 NO 时，表示`滑动动画结束`
- (void)scrollViewDidEndDragging:(UIScrollView *)scrollView willDecelerate:(BOOL)decelerate {
    NSLog(@"%s",__PRETTY_FUNCTION__);
}

// 该方法在「将要开始减速时」调用，（手指已经离开屏幕，但 scrollView 仍在减速滑动中）
// 仅当停止拖拽后继续移动时才会被调用
- (void)scrollViewWillBeginDecelerating:(UIScrollView *)scrollView {
    NSLog(@"%s",__PRETTY_FUNCTION__);
}

// 该方法在「已经结束减速时」调用
// 仅当停止拖拽后继续移动时才会被调用
- (void)scrollViewDidEndDecelerating:(UIScrollView *)scrollView {
    NSLog(@"%s",__PRETTY_FUNCTION__);
}

// 该方法用于返回是否允许点击状态栏让 scrollView 滑动到顶部，默认值为 YES
// 仅当 scrollsToTop 属性值为 YES 时才调用
- (BOOL)scrollViewShouldScrollToTop:(UIScrollView *)scrollView {
    return YES;
}

// 该方法在 scrollView 已经滑动到顶部时调用
// 仅当通过点击状态栏让 scrollView 滑动到顶部才调用
- (void)scrollViewDidScrollToTop:(UIScrollView *)scrollView {
    NSLog(@"%s",__PRETTY_FUNCTION__);
}

// 该方法在 -setContentOffset:animated:/-scrollRectVisible:animated: 方法动画结束时调用
// 仅当 animated 设置为 YES 时才调用
- (void)scrollViewDidEndScrollingAnimation:(UIScrollView *)scrollView {
    NSLog(@"%s",__PRETTY_FUNCTION__);
}

// 该方法在缩放比例发生变化时调用
- (void)scrollViewDidZoom:(UIScrollView *)scrollView {
    NSLog(@"%s",__PRETTY_FUNCTION__);
}

// 该方法用于返回参与缩放的子视图
// return a view that will be scaled. if delegate returns nil, nothing happens
- (nullable UIView *)viewForZoomingInScrollView:(UIScrollView *)scrollView {
    return nil;
}

// 该方法在将要开始缩放时调用
// called before the scroll view begins zooming its content
- (void)scrollViewWillBeginZooming:(UIScrollView *)scrollView withView:(nullable UIView *)view API_AVAILABLE(ios(3.2)) {
    NSLog(@"%s",__PRETTY_FUNCTION__);
}

// 该方法在已经结束缩放时调用
// scale between minimum and maximum. called after any 'bounce' animations
- (void)scrollViewDidEndZooming:(UIScrollView *)scrollView withView:(nullable UIView *)view atScale:(CGFloat)scale {
    NSLog(@"%s",__PRETTY_FUNCTION__);
}

// 该方法在 adjustedContentInset 发生变化时调用
- (void)scrollViewDidChangeAdjustedContentInset:(UIScrollView *)scrollView {
    NSLog(@"%s",__PRETTY_FUNCTION__);
}
```

### 参考
* [iOS控件详解之UIScrollView](http://www.jianshu.com/p/ba88e12eddc2)
* [UIScrollView 的用法](https://github.com/pro648/tips/wiki/UIScrollView%E7%9A%84%E7%94%A8%E6%B3%95)
* [UIScrollView 小结](https://juejin.im/post/6844903869944692743)
* [UIScrollView 的详细使用介绍和实现原理分析](https://juejin.im/entry/6844903444084424717)
