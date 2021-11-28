> 注：
> 本文翻译自 《iOS UICollectionView The Complete Guide 2nd Edition》
> 使用的翻译工具：<https://www.deepl.com/translator>


到目前为止，在本书中，我们集中讨论了集合视图中除了交互功能的各个方面。除了`UICollectionViewDelegate` 为你提供的一些基本的复制/粘贴功能之外，我们并没有关注用户如何与集合视图进行交互，除了基本的内容偏移变化。当然，我们已经通过导航按钮实现了根据用户交互改变布局，但这不是我所说的。我问的是这个问题。我们怎样才能在集合视图中直接添加交互性？我们如何才能做出真正沉浸式的界面？答案是手势识别器（Gesture Recognizers），这也是本章要讨论的重点。

我们要回顾一下四个代码示例，并为它们增加新的用户交互性。这就是真正卓越的应用程序的打磨。读完本章，你将拥有将这些同样的技术应用到自己的集合视图中的技能。



## 基础的手势识别器

让我们回顾 Better Survey 示例，本章的代码可以在 Improved Better Survey 项目下找到。我们将添加代码，当单元格被长按时，在单元格上方显示一个自定义菜单控制器，如图6.1所示。

![图6.1](https://blog-andy0570-1256077835.cos.ap-shanghai.myqcloud.com/site_Images/screenshot_01.jpeg)

删除旧的 `UICollectionViewDelegate` 中支持剪切/复制/粘贴部分的方法，我们不再需要它了。将清单 6.1 中所示的代码添加到你的 `viewDidLoad` 实现中。

```objc
UILongPressGestureRecognizer *longPressGestureRecognizer = [[UILongPressGestureRecognizer alloc] initWithTarget:self action:@selector(handleLongPress:)];
[self.collectionView addGestureRecognizer:longPressGestureRecognizer];
```

这里创建了一个长按手势识别器，并将其添加到我们的集合视图中。当你的视图设置好之后，设置你的手势识别器是很重要的。我们可以在 `loadView` 中进行，但我更喜欢 `viewDidLoad`，因为这是我们在视图加载后才需要的行为，而这正是 `viewDidLoad` 的作用。

还请注意，我们将手势识别器添加到集合视图本身，而不是每个单元格。这就减少了代码和内存中的对象。正如你很快会看到的，`UICollectionView` 提供了一个简单的方法来确定当前的点坐标位于哪个单元格上（如果有的话）。

现在我们已经设置好了手势识别器，我们需要对它做出响应。将清单6.2中所示的方法添加到您的视图控制器实现中。

```objc
-(void)handleLongPress:(UILongPressGestureRecognizer *)recognizer
{
    if (recognizer.state != UIGestureRecognizerStateBegan) return;
    
    // 获取手势识别器触摸时的点坐标，用该点坐标来定位它在集合视图中的 indexPath。
    CGPoint point = [recognizer locationInView:self.collectionView];
    NSIndexPath *indexPath = [self.collectionView indexPathForItemAtPoint:point];
    
    // 检查并确保长按手势的位置在 cell 上
    if (!indexPath) return;
    
    // 为 menuAction: 方法更新变量
    lastLongPressedIndexPath = indexPath;
    
    // 获取 cell 以定位菜单控制器（menu controller）要显示的位置
    UICollectionViewCell *cell = [self.collectionView cellForItemAtIndexPath:indexPath];
    
    // 创建一个自定义菜单项，以保存单元格所呈现的模型名称
    UIMenuItem *menuItem = [[UIMenuItem alloc] initWithTitle:[[self photoModelForIndexPath:indexPath] name] action:@selector(menuAction:)];
    
    // 配置共享菜单控制器并显示它
    UIMenuController *menuController = [UIMenuController sharedMenuController];
    menuController.menuItems = @[menuItem];
    [menuController setTargetRect:cell.bounds inView:cell];
    [menuController setMenuVisible:YES animated:NO];
}
```



每当手势识别器状态发生变化时，就会调用这个方法。你要注意关于这个方法的几个重要事项。首先，它检查识别器的状态。尽管这是一个长按手势识别器，而且它没有"changed "状态，但我们需要确保它处于"began"的状态（而不是例如"possible"这种状态）。一般来说，在使用手势识别器时，这是一个很好的做法。

接下来，它要找到用户正在点击的点坐标。我们必须检查返回的 index 索引；如果点击的坐标下没有单元格，它将为零。

我们将 index 索引保存在一个实例变量中，以便以后使用；我们需要记住最近长按的索引路径是哪个。最后，我们获取共享的 `UIMenuController` 单例，并从单元格本身显示一个自定义的 `UIMenuItem`。这将在单元格上显示剪切/复制/粘贴菜单。`menuAction:`选择器是必要的；它不能是 `nil`，但它需要在我们的类中的某个地方实现。我们在清单6.3中实现了 `menuAction:`。

```objc
-(void)menuAction:(id)sender
{
    // 获取最后一个长按的索引路径，用它来找到对应的模型，并将其复制到 pasteboard 粘贴板上。
    UIPasteboard *pasteboard = [UIPasteboard generalPasteboard];
    [pasteboard setString:[[self photoModelForIndexPath:lastLongPressedIndexPath] name]];
}
```

这个方法是必要的，因为它将在用户点击自定义菜单项时被调用。

如果你现在运行这个应用，它就会显得很糟糕，因为菜单控制器从未出现。为什么会出现这种情况呢？答案有点晦涩难懂。我们的视图控制器是 `UIViewController` 的子类，它扩展了 `UIResponder`。这个类中的方法被 `UIMenuController` 用来判断一个给定对象是否可以显示菜单控制器。具体来说，我们需要覆盖清单 6.4 中所示的`canBecomeFirstResponder`，因为默认的是 `NO`，以及`canPerformAction:withSender:`，因为我们已经创建了一个自定义的动作。

```objc
#pragma mark - UIResponder Overridden Methods

- (BOOL)canPerformAction:(SEL)selector withSender:(id)sender
{
    // 确保我们添加的菜单控制器让响应链知道它可以处理它自己的自定义菜单动作。
    if (selector == @selector(menuAction:)) {
        return YES;
    }
    
    return [super canPerformAction:selector withSender:sender];
}

-(BOOL)canBecomeFirstResponder
{
    // 必须覆盖，让菜单控制器知道它可以被处理。
    return YES;
}
```

现在，长按手势识别器将按预期工作。

如果你正在滚动自己的手势识别器，你可能会遇到 `UICollectionView` 内置识别器的干扰。在 `UIScrollView.h` 中查看 `tap` 和`panGestureRecognizer` 以及 `pinchGestureRecognizer` 属性。如果你在实现自己的手势识别器时遇到麻烦，可以设置一个`UIGestureRecognizerDelegate`，并使用 `require-GestureRecognizerToFail:` 设置一个失败的识别器链。



## 响应 Tap 点击事件

正如你所看到的，实现一个 Cover Flow 布局并不难。然而，我们目前的实现缺乏一点华丽的感觉。如果当点击一个非居中单元格时，集合视图能以该单元格为中心，那就更好了。这听起来很简单，但我发现，一些关于 `UICollectionView` 的奇怪行为使它变得有点棘手。

新的 Cover Flow 布局的示例代码叫做 Improved Cover Flow。让我们先在 `viewDidLoad` 中设置另一个基本的手势识别器，如清单 6.5 所示。

```objc
UITapGestureRecognizer *tapGestureRecognizer = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(handleTapGestureRecognizer:)];
[self.collectionView addGestureRecognizer:tapGestureRecognizer];
```

很好。在实现 `handleTapGestureRecognizer:` 方法之前，我们需要有一些方法来确定一个单元格是否居中了。让我们进入 `AFCoverFlowLayout` 类 ，在头和实现文件中添加以下公共方法，如清单6.6所示。

```objc
-(BOOL)indexPathIsCentered:(NSIndexPath *)indexPath
{
    CGRect visibleRect = CGRectMake(self.collectionView.contentOffset.x, self.collectionView.contentOffset.y, CGRectGetWidth(self.collectionView.bounds), CGRectGetHeight(self.collectionView.bounds));
    
    UICollectionViewLayoutAttributes *attributes = [self layoutAttributesForItemAtIndexPath:indexPath];
    
    CGFloat distanceFromVisibleRectToItem = CGRectGetMidX(visibleRect) - attributes.center.x;
    return fabs(distanceFromVisibleRectToItem) < 1;
}
```

为了确定一个单元格是否居中，我们依靠我们现有布局的方法来确定给定索引路径处 item 的属性，并检查该单元格与当前可见框架中心的距离是否小于一个小值（比如，1）。

接下来，我们实现我们的手势识别器目标方法（见清单6.7）。

```objc
-(void)handleTapGestureRecognizer:(UITapGestureRecognizer *)recognizer
{
    if (self.collectionView.collectionViewLayout != coverFlowCollectionViewLayout) return;
    if (recognizer.state != UIGestureRecognizerStateRecognized) return;
    
    CGPoint point = [recognizer locationInView:self.collectionView];
    NSIndexPath *indexPath = [self.collectionView indexPathForItemAtPoint:point];
    
    if (!indexPath) return;
    
    // 判断当前 indexPath 的单元格是否居中
    BOOL centered = [coverFlowCollectionViewLayout indexPathIsCentered:indexPath];
    if (centered) {
        UICollectionViewCell *cell = [self.collectionView cellForItemAtIndexPath:indexPath];
        
        // 执行翻转动画
        [UIView transitionWithView:cell duration:0.5f options:UIViewAnimationOptionTransitionFlipFromRight animations:^{
            cell.bounds = cell.bounds;
        } completion:nil];
    } else {
        CGPoint proposedOffset = CGPointMake(0, 0);
        if (UIInterfaceOrientationIsPortrait(self.interfaceOrientation)) {
            proposedOffset.x = indexPath.item * (coverFlowCollectionViewLayout.itemSize.width + coverFlowCollectionViewLayout.minimumLineSpacing);
        } else {
            proposedOffset.x = (indexPath.item - 1) * (coverFlowCollectionViewLayout.itemSize.width + coverFlowCollectionViewLayout.minimumLineSpacing);
        }
        
        CGPoint contentOffset = [coverFlowCollectionViewLayout targetContentOffsetForProposedContentOffset:proposedOffset withScrollingVelocity:CGPointMake(0, 0)];
        
        [self.collectionView setContentOffset:contentOffset animated:YES];
    }
}
```

我们要做的第一件事是检查并确保用户在 Cover Flow 布局中点击。然后检查确保手势识别器处于正确的状态；这在处理手势识别器时是很常见的。我们获取点击下的坐标点及其对应的 indexPath，并检查它是否为零，就像上次一样。

接下来，我们确定单元格是否已经居中。如果是的话，我们会执行一个类似于 iTunes 应用的 Cover Flow 的酷炫翻转动画。如果还没有居中，我们就通过调整内容偏移量，用动画让它居中。

我们不能依赖 `UICollectionView` 的`scrollToItemAtIndexPath:scrollToItemAtIndexPath:animated:` 方法，因为它将使用 item 的位置。

而不居中来执行去向的计算。其结果是你永远不会将项目居中。不幸的是，答案是计算内容偏移的位置，如果项目已经居中，并滚动到那里；我们在设置 `proposedOffset.x` 时就这样做。

这只是对单元格居中时的位置的估计；我们只需要接近到足够的程度，Cover Flow 布局就可以接管并重新调整它。这就是为什么我们调用 `targetContentOffsetForProposedContentOffset:withScrollingVelocity:`。它将会去调整我们的内容偏移量，使之精确到我们需要在屏幕中心显示单元格的偏移量。



## 捏合和平移支持

我们再来回顾最近一个可以改进的例子：环形布局。带有手势支持的环形布局的代码被标注为 Improved Circle Layout。

我们将在圆圈上添加一个捏手势识别器，让用户重新定位和调整圆圈的大小，我们的项目就落在这个圆圈上。幸运的是，我们的布局已经暴露了中心和半径属性，所以从视图控制器中调整它们很容易。

目前，我们在 `prepareLayout` 中设置了圆的中心和半径。我们需要删除这些，因为每次布局无效时都会调用这个方法。取而代之的是，我们将把这些放在视图控制器的 `viewDidLoad` 方法中。我们还将在这里设置我们的捏合识别器，如清单 6.8 所示。

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    
		// ...
    
    // 初始化环状布局属性
    CGSize size = self.collectionView.bounds.size;
    self.circleLayout.center = CGPointMake(size.width / 2.0, size.height / 2.0);
    self.circleLayout.radius = MIN(size.width, size.height) / 2.5;
    
    // 创建捏合手势识别器
    UIPinchGestureRecognizer* pinchRecognizer = [[UIPinchGestureRecognizer alloc] initWithTarget:self action:@selector(handlePinchGesture:)];
    [self.collectionView addGestureRecognizer:pinchRecognizer];
}
```

我们还应该覆写 `center` 和 `radius` 属性的设置方法，以失效并重新布局，如清单6.9所示。

```objc
-(void)setRadius:(CGFloat)radius {
    _radius = radius;
    
    [self invalidateLayout];
}

-(void)setCenter:(CGPoint)center {
    _center = center;
    
    [self invalidateLayout];
}
```

太好了，我们就快完成了。真的。布局已经使用 `center` 和 `radius` 属性来布局单元格，所以我们不需要再修改任何布局代码。我们需要做的就是编写手势识别器方法，如清单6.10所示。

```objc
- (void)handlePinchGesture:(UIPinchGestureRecognizer *)recognizer {
    static CGPoint initialLocation;
    static CGPoint initialPinchLocation;
    static CGFloat initialRadius;
    
    if (self.collectionView.collectionViewLayout != self.circleLayout) return;
    
    if (recognizer.state == UIGestureRecognizerStateBegan) {
        initialLocation = self.circleLayout.center;
        initialPinchLocation = [recognizer locationInView:self.collectionView];
        initialRadius = self.circleLayout.radius;
    } else if (recognizer.state == UIGestureRecognizerStateChanged) {
        CGPoint newLocation = [recognizer locationInView:self.collectionView];
        
        CGPoint translation;
        translation.x = initialPinchLocation.x - newLocation.x;
        translation.y = initialPinchLocation.y - newLocation.y;
        
        CGFloat newScale = [recognizer scale];
        
        self.circleLayout.center = CGPointMake(initialLocation.x - translation.x,
                                               initialLocation.y - translation.y);
        self.circleLayout.radius = initialRadius * newScale;
    }
}
```

当用户开始做手势时，我们会在一些静态变量中 "记住 "原始的捏合位置、圆心和圆半径。这些变量将在方法调用的每次迭代中保留其值。然后，每当手势识别器改变值时，我们就会重新计算中心和半径的新值，依靠重写的设置器为我们无效布局。



我们再来看一个捏合布局。Mark Pospesel 写的堆栈布局已经包含了捏开堆栈的代码（就像iPad上的Photos应用对相册一样）。

清单6.11显示了 `UICollectionView` 的一个 bug，当改变布局时，它不会删除补充视图和装饰视图；所以现在，我们需要自己动手。

```objc
-(void)handlePinch:(UIPinchGestureRecognizer *)recognizer
{
    if (self.collectionView.collectionViewLayout != self.stackLayout)
        return;
    
    if (recognizer.state == UIGestureRecognizerStateBegan) {
        CGPoint initialPinchPoint = [recognizer locationInView:self.collectionView];
        NSIndexPath* pinchedCellPath = [self.collectionView indexPathForItemAtPoint:initialPinchPoint];
        if (pinchedCellPath) {
            [self.stackLayout setPinchedStackIndex:pinchedCellPath.section];
        }
    } else if (recognizer.state == UIGestureRecognizerStateChanged) {
        self.stackLayout.pinchedStackScale = recognizer.scale;
        self.stackLayout.pinchedStackCenter = [recognizer locationInView:self.collectionView];
    } else {
        if (self.stackLayout.pinchedStackIndex >= 0) {
            if (self.stackLayout.pinchedStackScale > 2.5) {
                [self.collectionView setCollectionViewLayout:self.flowLayout animated:YES];
                [self.navigationItem setLeftBarButtonItem:[[UIBarButtonItem alloc] initWithTitle:@"Back" style:UIBarButtonItemStyleBordered target:self action:@selector(goBack)] animated:YES];
            } else {
                // collapse items back into stack
                NSMutableArray *leftoverViews = [NSMutableArray array];
                for (UIView *subview in self.collectionView.subviews) {
                    // Find all the supplementary views
                    if ([subview isKindOfClass:[AFCollectionViewHeaderView class]]) {
                        [leftoverViews addObject:subview];
                    }
                }
                
                self.stackLayout.collapsing = YES;
                [self.collectionView performBatchUpdates:^{
                    self.stackLayout.pinchedStackIndex = -1;
                    self.stackLayout.pinchedStackScale = 1.0;
                } completion:^(BOOL finished) {
                    self.stackLayout.collapsing = NO;
                    // remove them from the view hierarchy
                    for (UIView *subview in leftoverViews)
                        [subview removeFromSuperview];
                }];
            }
        }
    }
}
```

## Layout-to-Layout 过滤

iOS 7 通过 `UICollectionViewController` 引入了一个新概念：layout-tolayout 过渡。这些都是在一个集合视图布局和另一个集合视图布局之间进行插值的方便、简单的方法。这种技术的用例是当你需要一个非交互式的 "推 "类型的过渡（即，点击单元格以查看更多细节）。请注意，iOS 7从左边缘滑动的手势仍将以其交互方式工作。

要使用布局到布局的过渡，你需要在一个导航控制器中使用两个`UICollectionViewControllers`。其中一个将把另一个推送到导航栈上。就在推送那个第二个视图控制器之前，将 `useLayoutToLayoutNavigationTransitions` 设置为 `YES`。第二个视图控制器的集合视图将使用与第一个控制器相同的数据源和委托。(在我们的例子中会出现这种情况，这通常是第一个视图控制器本身)。委托出口本身被设置为第一个视图控制器的集合视图的委托，但消息被转发到第二个视图控制器本身。苹果是怎么做的还不清楚，文档中也没有说明太多。

我们的实现相当简单。我们要有一个基本的视图控制器，并在 app delegate 中设置一个基本的流程布局，如清单6.12所示。

**无源码**

...



## UIKit Dynamics

