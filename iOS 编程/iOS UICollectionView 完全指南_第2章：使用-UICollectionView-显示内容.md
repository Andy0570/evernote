> 注：
> 本文翻译自 《iOS UICollectionView The Complete Guide 2nd Edition》
> 使用的翻译工具：<https://www.deepl.com/translator>

现在，你已经了解了如何在遵循 Model-View-Control (MVC) 设计模式的前提下，在 iOS 应用中使用集合视图。是时候尝试新东西了：代码。本章一开始很简单，展示了如何使用 `storyboards` 或 `.xibs` 来设置集合视图，然后告诉你如何在代码中设置它们。集合视图扩展了它的父类 `UIScrollView`，所以本章简单的绕了一圈，展示如何使用 `UIScrollViewDelegate` 来发挥你的优势。在结束关于性能的案例研究之前，你将开始使用单元格重用来定制实际的内容以显示给你的用户。



## 使用代码和 Storyboards 进行设置

传统意义上，`.xib` 文件用于为 OS X 和 iOS 应用设置并布局 UI 代码。这些文件是你的界面的 "冻结 "版本，会在运行时解冻。`.xibs` 文件的好处是，它们很容易用来创建基本的接口；通常每个`.xib` 都有一个 `UIViewController` 的实例。

Storyboards 首次被引入是在 2011年的 iOS 5 中，它使开发者能够直观地布局视图控制器之间的交互。开发者不仅可以可视化视图控制器之间的连接，还可以定义整个应用如何从一个视图控制器过渡到另一个视图控制器。Storyboards 的关键之处在于它们的效率；一个巨大的 `.xib` 文件，必须完全加载到内存中，会延迟你的应用程序启动的时间。Storyboards 可以有效地只懒加载必要的视图控制器。

当然，任何可以在 `.xib` 文件或 storyboard 中做的事情都可以使用冷冰冰的硬代码来实现。如果你正在将集合视图集成到你现有的应用程序中，该应用程序使用 `.xib` 文件或 storyboard，继续使用它们可能会很方便。然而，由于集合视图需要使用代码来进行布局，因此完全避免使用 `.xib` 和 storyboard 往往更容易。尽管如此，本章还是阐述了如何使用 storyboard 设置上一章的集合视图，然后再演示只使用代码进行设置。

使用 "Single View template" 模板创建一个新的 Xcode 项目。确保 "Use Storyboards" 被选中。打开 `Main.storyboard` 文件，删除已经存在的视图控制器。从右侧窗格的对象库中拖动一个集合视图控制器到空画布上，如图2.1 所示。

（略）

你现在就可以运行这个应用，它可以正常工作，但会很无聊。storyboard 文件中已经默认设置了集合视图的委托和数据源插座连接，指向你的集合视图控制器。下一步是自定义该视图控制器的实际功能。这部分很简单，因为你只需要复制第1章 "理解 iOS 中的 Model-View-Controller "中的现有代码。

打开你的视图控制器的头文件，改变它继承自哪个类（将 `UIViewController` 改为 `UICollectionViewController`）。然后把上一章的实现文件完整地复制过来。最后，重要的一步是告诉你的 storyboard 应该使用哪个视图控制器。点击 storyboard 中的集合视图控制器，打开 "Identity Inspector"。在 Class 的地方，你会看到默认的占位符是 `UICollectionViewController`。无聊! 用你的视图控制器的名字来代替--在我的例子中，它是 `AFViewController`。

这一步至关重要，它是 storyboard 如何知道在布局集合视图时要执行什么代码的原因。运行你的应用程序，你会看到和第1章一样的输出。

使用 storyboard 或 `.xibs` 文件时，你可以在不编写任何代码的情况下更改集合视图的视觉显示样式。在 storyboards 中选择集合视图，然后打开 "Attributes Inspector"。在这里，你可以将集合视图的滚动方向从默认的垂直方向改为水平方向。你还可以更改集合视图属于其父类 `UIScrollView` 的属性。将滚动指示器 "样式 "改为白色，使滚动指示器在黑色背景下可见。

打开 "Size Inspector"，你可以改变集合视图布局的属性，如图2.2所示（集合视图将这些属性抽象到它们的布局对象中，更多内容请阅读第3章 "内容的上下文"）。在这里，你可以改变单元格的大小，默认情况下是 50*50。将宽度降为 20，并保持高度设置为 50。页眉和页脚大小还不能用，因为你还没有使用页眉或页脚。


<img src="https://blog-andy0570-1256077835.cos.ap-shanghai.myqcloud.com/site_Images/Jietu20200825-175533%402x.png" alt="图2.2" style="zoom:50%;" />


你可以在 "Size Inspector" 中的 "Min Spacing " 部分更改集合视图中单元格之间的距离。这只是最小距离；默认布局（称为 "流"）确保单元格之间的距离最小。通过 "Size Inspector" 中的 "Section Insets" 区域，你可以指定整个 Section 段周围的距离。(请记住，到目前为止，你只有一个 Section)。您将在第 3 章中更仔细地了解 "section insets" 属性，所以现在不用担心具体细节。我个人最讨厌内容周围的边距太小，所以把 "section insets" 各个方向上的值设置为 10。

<img src="https://blog-andy0570-1256077835.cos.ap-shanghai.myqcloud.com/site_Images/100851.png" style="zoom:50%;" />

上图示例中，我们设置了该集合视图的 section 的边缘插入量为 `{10,10,10,10}`，默认值为 `{0,0,0,0}`。

运行应用程序，看看集合视图中的视觉差异。它应该类似于图2.3。

<img src="https://blog-andy0570-1256077835.cos.ap-shanghai.myqcloud.com/site_Images/screenshot-1.PNG" style="zoom:40%;" />

一点都不赖。不要担心系统状态栏在我们的内容页面上是可见的，这是 iOS 7 的默认值。我们稍后会通过将集合视图控制器放在导航控制器里面来解决这个问题。图 2.3 的问题是，只有集合视图布局的部分属性可以通过 storyboards 或 `.xib` 文件访问。此外，如果你在代码中覆盖了你在 storyboard 中设置的属性，或者你忘记了你在 storyboards 中设置了一些东西，这可能会导致调试上的困难。出于这个原因，我强烈建议对集合视图使用纯代码的方法。

你也可以**通过只使用代码的方式重新创建你的界面**。使用空应用程序模板创建一个新的 Xcode 项目。（对于从未从空模板创建过应用程序的人来说，这可能是一个很大的步骤）。使用 File、New、File 或 ⌘N 创建一个新文件。选择 Objective-C 类，并将其命名为 `AFViewController` 之类。在 Subclass 的字段中，输入`UICollectionViewController`。确保不要选择 User Interface 的 With XIB。

打开 `AppDelegate.m` 文件，并添加一个 `#import` 语句来导入新视图控制器的头文件。将实现改为清单2.1中的代码。

```objc
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions { 
    self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];

    UICollectionViewFlowLayout *collectionViewLayout = [[UICollectionViewFlowLayout alloc] init];
    collectionViewLayout.scrollDirection = UICollectionViewScrollDirectionHorizontal;
    collectionViewLayout.sectionInset = UIEdgeInsetsMake(10, 10, 10, 10); collectionViewLayout.itemSize = CGSizeMake(20, 50); self.window.rootViewController = [[AFViewController alloc] initWithCollectionViewLayout:collectionViewLayout];
    self.window.backgroundColor = [UIColor whiteColor]; [self.window makeKeyAndVisible];

    return YES;
}
```

下一步，打开视图控制器的实现文件，并在 `viewDidLoad` 方法中添加下面代码：

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    
    self.collectionView.indicatorStyle = UIScrollViewIndicatorStyleWhite;
}
```

（其他设置与上一个示例相同）

构建并运行应用程序，你会发现你使用 storyboards 定制的所有东西都已经用代码复制了。击掌!

在你深入了解集合视图和布局内容之前，下面的部分将带你快速转移讨论 `UIScrollView`。


## UIScrollView：简要概述

`UICollectionView` 是 `UIScrollView` 的子类，和 `UITableView` 很类似。与`UICollectionView` 的继承关系类似，`UICollectionViewDelegate` 协议也遵守 `UIScrollViewDelegate` 协议。在实际操作中，这意味着如果一个对象是集合视图的委托者，它就会收到回调通知，触发 `UICollectionViewDelegate` 事件以及 `UIScrollViewDelegate` 事件。

> 注：也就是说，如果一个对象是 `UICollectionView` 实例对象的委托对象，那么这个委托对象既遵守 `UICollectionViewDelegate` 协议中的方法，同时也自动遵守 `UIScrollViewDelegate` 协议中的方法。因为 `UICollectionViewDelegate` 协议继承自  `UIScrollViewDelegate` 协议，它是 `UIScrollViewDelegate` 协议的子协议。

`UIScrollView` 是 UIKit 中的一个多功能类，从 iOS 2.0 的时候就已经存在了。它为开发人员提供了一种友好的方式来滚动内容，无论是电子邮件列表、应用程序的网格，还是一张照片。如果你可以在任何给定的应用程序中滚动一些东西，那么该应用程序有可能使用了 `UIScrollView` 滚动视图。

滚动视图给用户一种熟悉的感觉，让任何使用滚动视图的应用看起来更像是属于 iOS 系统，而不像其开发者自己写的滚动视图。滚动视图以极少的工作为开发者提供了很大的权力，开发者需要做的就是设置滚动视图并为其添加子视图。此外，你还可以依靠苹果已经为你做的工作，比如模拟物理学和减速。看看一个例子，在这个例子中，用户可以滚动查看比屏幕上能同时容纳的更多内容。

用单视图模板创建一个新的 Xcode 项目。将一张大图片复制到项目中，打开主视图控制器的实现文件。用清单2.3中的实现替换 `viewDidLoad`。

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    
    UIImage *image = [UIImage imageNamed:@"cat.jpg"];
    UIImageView *imageView = [[UIImageView alloc] initWithImage:image];
    imageView.frame = CGRectMake(0, 0, image.size.width, image.size.height);
    
    UIScrollView *scrollView = [[UIScrollView alloc] initWithFrame:self.view.bounds];
    scrollView.contentSize = image.size;
    scrollView.autoresizingMask = UIViewAutoresizingFlexibleWidth | UIViewAutoresizingFlexibleHeight;
  
    [scrollView addSubview:imageView];
    [self.view addSubview:scrollView];
}
```

运行应用程序，你会看到类似于图 2.4 的显示效果；由于图片太大了，一次无法在屏幕上完整显示，但用户可以围绕图像滚动以查看全部内容。(请注意滚动指示器。) 使这一切发挥作用的魔法是 `contentSize` 属性。这是一个 `CGSize` 值，表示可滚动区域的尺寸大小（以点为单位）。它的默认值为零，而且使用任何滚动视图时必须设置这个属性的值，即使内容尺寸小于滚动视图自身的尺寸。

<img src="https://blog-andy0570-1256077835.cos.ap-shanghai.myqcloud.com/site_Images/034118.png" alt="图 2.4" style="zoom:50%;" />



当滚动视图知道它所显示的内容的尺寸大小时，它就会滚动。内容尺寸（`contentSize`）可以随时改变。

图2.5 展示了内容尺寸的概念。照片左上角的亮色矩形区域，定义了应用程序第一次启动时图像的可见部分，这就是滚动视图的尺寸（the size of the scroll view ），用虚线表示。实线代表滚动视图的内容大小（the content size of the scroll view）。

<img src="https://blog-andy0570-1256077835.cos.ap-shanghai.myqcloud.com/site_Images/034836.png" alt="图 2.5" style="zoom: 67%;" />

当用户滚动 scrollView 时，用户可见的内容区域会发生变化。内容视图在滚动视图中的位置称为内容偏移，由 `contentOffset` 属性表示，是一个 `CGPoint` 值。该属性由可见区域的原点（左上角）到内容原点的距离定义。图2.6 用白色虚线演示了内容偏移。内容大小保持不变，但内容偏移量会改变，以响应用户的交互。

<img src="https://blog-andy0570-1256077835.cos.ap-shanghai.myqcloud.com/site_Images/%E5%9B%BE%E7%89%87%E6%9D%A5%E8%87%AA%20iOS%20UICollectionView%20The%20Complete%20Guide%202nd%20Edition.2014.%E8%8B%B1%E6%96%87%E7%89%88%EF%BC%8C%E7%AC%AC%2031%20%E9%A1%B5-1.png" alt="图 2.6" style="zoom:67%;" />



内容偏移可以通过编程方式改变，`contentOffset` 属性是可读可写的。更有趣的是，你可以使用 `setContentOffset:animated:` 方法以动画方式对内容偏移的变化进行处理。这将 "移动 "滚动视图，就像用户自己移动它一样。内容偏移也可以用 `scrollRectToVisible:animated:` 方法来改变，但这更多的是用于缩放而不是简单的滚动。

关于 scrollView，我想说的最后一件事是 `contentInset` 属性。这是一个 `UIEdgeInset` 值，表示滚动视图内容周围应该 "填充" 的区域。将`contentInset` 属性设置为 `UIEdgeInsetsMake(10, 10, 10, 10)`，将在 scrollView 的内容周围创建一个10pt 的边距。边缘插入值也可以是负值；这将代表滚动视图内容周围不能被用户看到的区域（除非她滚动过滚动视图的边缘）。试着玩玩 `contentInset`，看看它是如何工作的。

`contentInset` 属性是一个广泛使用的属性，经常被用于 `UITableView` 和自定义下拉刷新控件中。如果你在视图控制器的顶部有一个导航栏，并且 `wantsFullScreenLayout` 设置为 `YES`，那么它也很有用。边缘插入量的上边界的值等于状态栏和导航栏的高度。

这就是 `UIScrollView` 的三个主要组件：`contentSize`、`contentOffset `和 `contentInset`。现在，在本章继续讨论更多的集合视图之前，是时候对滚动视图委托（`UIScrollViewDelegate`）进行快速讨论了。

* `contentSize` 用来标识 `UIScrollView` 的可滚动范围；
* `contentOffset ` 用来设置 `UIScrollView` 的视图原点与当前可视区域左上角的距离；
* `contentInset` 用于设置边缘插入量，或者说，额外的视图内边距；

`UIScrollViewDelegate` 中有三组方法：响应拖动和滚动的方法，响应缩放的方法，以及响应由代码显式启动的滚动动画的方法（见表2.1）。你将只处理第一组和最后一组，因为集合视图不使用 `UIScrollView` 的缩放功能。

### 表 2.1 有用的 `UIScrollViewDelegate` 方法

| 方法名                                                       | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `scrollViewDidScroll:`                                       | 当 scrollView 的内容偏移（`contentOffset`）发生变化时，就会被调用，可以是代码触发的变化，也可以是响应用户交互触发的变化。可用于自定义的下拉刷新控件中。 |
| `scrollViewWillBeginDragging:`                               | 当 scrollView 即将被用户拖动时调用。可能的用途是禁止滚动视图的更新（暂停一些复杂操作），因为这可能会影响滚动的流畅性。 |
| `scrollViewWillEndDragging: withVelocity: targetContentOffset:` | 当用户在拖动后从 scrollView 上抬起手指时，就会被调用。第二个参数表示结束拖动时的滚动速度，以点/秒为单位，表示当用户抬起手指时，滚动视图的速度。第三个参数是一个 `CGPoint` 类型的指针类型，代表 scrollView 将滚动到的位置。修改该 `CGPoint` 值就会改变滚动视图的滚动位置。可能的用途是计算当滚动动画结束时什么内容将是可见的，并从应用程序编程接口（API）中预取。 |
| `scrollViewDidEndDragging: willDecelerate:`                  | 当用户在 scrollView 上拖动后抬起手指时，就会被调用。第二个参数表示 scrollView 是否以动画形式停止减速，或者当用户抬起手指时是否已经停止。只要第二个参数是 NO，可能的用途包括重启在`scrollViewWillBeginDragging:` 中停止的任何暂停的计算。（注：这个 decelerate 参数表示滚动视图是**缓慢减速**的还是戛然而止**立即减速**的） |
| `scrollViewShouldScrollToTop:`                               | 当操作系统需要确定当用户点击系统状态栏时，是否应该将滚动视图以动画方式自动滚动到顶部时，就会调用该方法。每次只有一个可见的滚动视图应该从这个方法返回 YES。 |
| `scrollViewDidScrollToTop:`                                  | 在滚动视图因用户点击状态栏而滚动到顶部后调用。               |
| `scrollViewWillBeginDecelerating:`                           | 当滚动视图即将开始减速动画时调用。                           |
| `scrollViewDidEndDecelerating:`                              | 在滚动视图的减速动画完成后调用。可能的用途包括重新启动在 `scrollViewWillBeginDragging:` 方法中暂停的复杂计算。 |
| `scrollViewDidEndScrollingAnimation:`                        | 当滚动视图的内容偏移量（`contentOffset`）以动画方式变化完成后调用。只有当内容偏移量是以编程方式改变并且启用了显式动画时，才会在委托者上调用该方法。 |

在本书以后更进阶的章节和一些案例研究中，你会用到一些滚动视图的委托方法。它们是解决许多问题的有用工具，你应该了解它们。



## UICollectionViewCell 的重用：如何以及为何重用

`UICollectionView` 使用一种节省内存的方案来配置各个单元格的显示。正如苹果公司的一位软件工程师所说的那样，"创建并分配内存十分昂贵"。他的意思是，如果你做了很多为新的变量创建并分配内存的操作，就非常消耗系统内存。`UICollectionView` 的做法非常聪明：它重用不再显示的单元格。

>  Note
>
> 对于熟悉 `UITableView` 的人来说，这应该听起来很熟悉。在 iOS 6 中，苹果将 `UITableView` 最好的部分做成了 `UICollectionView`。许多东西看起来很熟悉，但你可能会对很多新东西感到惊讶。
>
> 这和列表中 `UITableViewCell` 的重用原理类似。

`UICollectionView` 依靠它的 `dataSource` 告诉它要显示多少个单元格，并在向用户展示之前对每个单元格进行配置。在滚动时，这需要非常快的速度，这就是为什么单元格需要重用的原因。下面解释一下具体发生了什么。

对于不同的单元格显示类型，你应该使用不同的单元格重用标识符。重用标识符是一个 `NSString` 类型的字符串，你通常将其存储为一个静态变量。在具有该重用标识符的任何单元格能够显示之前，它需要在集合视图中注册。这与 `UITableView` 有很大的不同。你通常会在 `viewDidLoad` 中注册单元格，而不会在以后重新注册它们。

当注册一个单元格时，你可以提供一个 `UINib` 实例或一个 `Class` 类。我更喜欢 `Class` 类而不是 nib，因为它能让我对布局和性能有更多的控制。

使用 `registerClass:forCellWithReuseIdentifier:` 或 `registerNib:forCellWithReuseIdentifier:` 方法注册单元格。自此，每当调用 `dequeueReusableCellWithReuseIdentifier:forIndexPath:` 方法时。系统会保证你有一个与你的重用标识符相对应的已分配和初始化好的单元格（见图2.7）。

<img src="https://blog-andy0570-1256077835.cos.ap-shanghai.myqcloud.com/site_Images/%E6%9C%AA%E5%91%BD%E5%90%8D%E6%96%87%E4%BB%B6-3.png" alt="图 2.7" style="zoom:50%;" />

这与 `UITableView` 稍有不同，`UITableView` 在历史上要求开发人员在尝试去序列化一个单元格时首先检查返回值是否为 `nil`（尽管现在它也支持这种先注册再使用的新方法了）。在集合视图中，系统会保证为你返回一个有效可使用的单元格。

如果你的集合视图只有 20 个单元格同时在屏幕上可见，那么你的集合视图只分配了 20 个单元格；当一个单元格滚动到屏幕外时，它将被添加到重用队列中，以便再次重用。这种技术可以让应用程序在滚动浏览具有数百或数千个单元格的集合视图时，保持极低的内存占用和极高的帧率。

本书中的大多数例子，以及集合视图的大多数实际用途，都只显示一种类型的单元格，因此只有一个重用标识符。如果你要显示不止一种类型的单元格，那么拥有不止一种类型的标识符是完全合理的。


## 向用户展示内容

好吧！你已经完成了 MVC 的一章和 `UICollectionView` 的半章基础知识。现在是时候看一些代码了。

你将创建一个基本的应用程序，向用户显示一些自定义内容。这将是一个 iPad 应用，所以你可以使用非常大的单元格。一开始你要做的是建立一个基本的集合视图，让用户可以通过加号按钮添加新的单元格，单元格会显示它们被添加的时间。这只是为后面的内容做热身。

使用 Empty Application 模板创建一个新的 Xcode 项目。创建一个新的文件，一个父类为 `UICollectionViewController` 的 Objective-C 类，并给它一个合适的名字。在你的应用程序委托的实现文件中，`#import` 视图控制器的头，并创建一个视图控制器的实例，作为导航控制器的根视图控制器，即窗口的根视图控制器。

```objc
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];
    self.window.backgroundColor = [UIColor whiteColor];
    
    UICollectionViewFlowLayout *flowLayout = [[UICollectionViewFlowLayout alloc] init];
    AFViewController *viewController = [[AFViewController alloc] initWithCollectionViewLayout:flowLayout];
    
    UINavigationController *navigationController = [[UINavigationController alloc] initWithRootViewController:viewController];
    navigationController.navigationBar.barStyle = UIBarStyleBlack;
    self.window.rootViewController = navigationController;
    
    [self.window makeKeyAndVisible];
    return YES;
}
```

你会使用到 `UINavigationController`，因为它免费提供了很多好东西。在本例中，你得到了一个很酷的导航栏，你可以在上面加入按钮。这个 `applicationDidFinishLaunchingWithOptions:` 的实现比本章前面的例子更轻量级；这次你将更接近遵循一些 "最佳实践"。app delegate 只是为视图控制器创建了基本的东西，它还进一步定制了自己。

创建一个新的 Objective-C 类，它继承 `UICollectionViewCell`。你还不打算给它添加任何代码。你只需要在视图控制器的实现文件中`#import` 导入即可。

打开视图控制器的实现文件，用一些指示性的值创建一个静态的NSString 实例；你将用它作为你的重用标识符。添加两个实例变量。一个是代表模型的 `NSMutableArray`，另一个是 `NSDateFormatter`，你将用它来格式化内容给用户。

```objc
#import "AFCollectionViewCell.h"

@interface AFViewController ()

@end

static NSString *CellIdentifier = @"Cell Identifier";

@implementation AFViewController
{
    //This is our model
    NSMutableArray *datesArray;
    NSDateFormatter *dateFormatter;
}
```

接下来，在 `viewDidLoad` 方法中创建并初始化一个空模型（你的datesArray）和一个日期格式化对象的实例。同时将你的布局和集合视图配置成漂亮的样子，通过重用标识符注册你的 UICollectionViewCell 子类，并为你的导航栏添加一个按钮。

```objc
- (void)viewDidLoad
{
    [super viewDidLoad];
    
    // 实例化模型
    datesArray = [NSMutableArray array];
    dateFormatter = [[NSDateFormatter alloc] init];
    [dateFormatter setDateFormat:[NSDateFormatter dateFormatFromTemplate:@"h:mm:ss a" options:0 locale:[NSLocale currentLocale]]];
    
    // 初始化集合视图布局
    UICollectionViewFlowLayout *flowLayout = (UICollectionViewFlowLayout *)self.collectionView.collectionViewLayout;
    flowLayout.minimumInteritemSpacing = 40.0f;
    flowLayout.minimumLineSpacing = 40.0f;
    flowLayout.sectionInset = UIEdgeInsetsMake(10, 10, 10, 10);
    flowLayout.itemSize = CGSizeMake(200, 200);
    
    // 配置集合视图
    [self.collectionView registerClass:[AFCollectionViewCell class] forCellWithReuseIdentifier:CellIdentifier];
    self.collectionView.indicatorStyle = UIScrollViewIndicatorStyleWhite;
    
    // 配置导航栏按钮
    UIBarButtonItem *addButton = [[UIBarButtonItem alloc] initWithBarButtonSystemItem:UIBarButtonSystemItemAdd target:self action:@selector(userTappedAddButton:)];
    self.navigationItem.rightBarButtonItem = addButton;
    self.navigationItem.title = @"Our Time Machine";
}
```

真棒，你现在就可以运行这个应用程序，但你看到的只是一个空屏幕，上面有一个加号按钮和一个标题。所以，在编写你的集合视图单元子类之前，先完成视图控制器的代码。你需要实现你的`UICollectionViewDataSource` 方法。

```objc
#pragma mark - UICollectionViewDataSource Methods

-(NSInteger)collectionView:(UICollectionView *)collectionView numberOfItemsInSection:(NSInteger)section
{
    return datesArray.count;
}

-(UICollectionViewCell *)collectionView:(UICollectionView *)collectionView cellForItemAtIndexPath:(NSIndexPath *)indexPath
{
    AFCollectionViewCell *cell = (AFCollectionViewCell *)[collectionView dequeueReusableCellWithReuseIdentifier:CellIdentifier forIndexPath:indexPath];
    
    cell.text = [dateFormatter stringFromDate:datesArray[indexPath.row]];
    
    return cell;
}
```

现在，这将引发一个编译器错误。不过不要担心。在你写完剩下的代码后，它将会工作。你需要一个方法来响应你的 add 按钮。创建两个方法：一个用于设置你在 `viewDidLoad` 中给导航栏按钮的选择器名称，另一个是你可以在代码中的任何地方调用的方法，以便向 `datesArray` 中添加新的日期。

```objc
#pragma mark - User Interface Interaction Methods

-(void)userTappedAddButton:(id)sender {
    [self addNewDate];
}

#pragma mark - Private, Custom methods

-(void)addNewDate {
    // performBatchUpdates: 批量更新集合视图
    [self.collectionView performBatchUpdates:^{
        //create a new date object and update our model
        NSDate *newDate = [NSDate date];
        [datesArray insertObject:newDate atIndex:0];
        
        //update our collection view
        [self.collectionView insertItemsAtIndexPaths:@[[NSIndexPath indexPathForItem:0 inSection:0]]];
    } completion:nil];
}
```



你在 `UICollectionView` 上调用 `performBatchUpdates:completion`: 执行批量更新。这可以让你免费获得动画（由你的布局类定义；更多内容在第3章）。

现在你要做的就是编写你的 `UICollectionViewCell` 子类。转到你之前创建的头文件。你要给它一个单一的 `NSString` 属性。

```objc
@interface AFCollectionViewCell : UICollectionViewCell

@property (nonatomic, copy) NSString *text;

@end
```

现在你的编译器将停止抱怨，但如果你运行应用程序，将不会发生任何真正有趣的事情。打开单元格的实现文件，添加一个 `UILabel` 实例变量。用以下代码实现覆盖 `initWithFrame`: 方法。

```objc
@implementation AFCollectionViewCell
{
    UILabel *textLabel;
}

#pragma mark - Initialization

- (id)initWithFrame:(CGRect)frame
{
    if (!(self = [super initWithFrame:frame])) return nil;
    
    self.backgroundColor = [UIColor whiteColor];
    
    textLabel = [[UILabel alloc] initWithFrame:self.bounds];
    textLabel.textAlignment = NSTextAlignmentCenter;
    textLabel.font = [UIFont boldSystemFontOfSize:20];
    [self.contentView addSubview:textLabel];
    
    return self;
}
```

接下来，覆盖文本属性来更新标签。你还将覆盖 `UICollectionViewCell` 的一个重要方法，称为`prepareForReuse`。

```objc
#pragma mark - Overriden UICollectionViewCell methods

-(void)prepareForReuse
{
    [super prepareForReuse];
    
    self.text = @"";
}

#pragma mark - Overriden properties

-(void)setText:(NSString *)text
{
    _text = [text copy];
    
    textLabel.text = self.text;
}
```

这将用被设置为文本属性的字符串更新单元格的标签。在`prepareForReuse` 方法中，你调用 `super` 关键字（非常重要！），然后将你的文本设置为空字符串。这一点真的很重要，你需要尽可能地将你的单元格重置到它的起始或中性状态。否则，集合视图的数据源可能会忘记重置部分数据，你可能最终会得到一个不一致和混乱的用户界面。

运行应用程序，你会看到一个空屏幕。点击 "加号 "按钮，在收集视图中添加一个新单元格。请注意，当一个新单元格被添加到集合视图的顶部时，你会得到一个动画（见图2.8）。很好！该应用还自适应屏幕旋转。

<img src="https://blog-andy0570-1256077835.cos.ap-shanghai.myqcloud.com/site_Images/070836.png" alt="图2.8" style="zoom:50%;" />







我不想让这个教程听起来像个关于 MVC 的破纪录，但重要的是要注意，单元格并不知道它在显示什么；它传递了一个字符串，而这个字符串恰好包含了一个与模型对应的日期。重要的是，你并没有向它传递 `NSDate` 对象本身。

现在，你有了一个基本的集合视图示例，再仔细看看 `UICollectionView `类本身。单元格有两个重要的布尔属性：**选中**和**高亮**。高亮状态完全取决于用户的交互；当用户的手指按住一个单元格时，它就会自动变成高亮状态。单元格的选中则不那么短暂；当用户抬起手指时，单元格就会被选中（如果集合视图支持选择）。单元格会一直被选中，直到你写的一些代码将其取消，或者直到用户再次点击它们。当被点击成为选中或取消选中时，单元格会暂时高亮。这些属性的设置器可以（并且经常）从动画块中调用。在覆盖它们的实现时要注意，你所做的改变很可能会被隐式动画化。

选中和高亮可能会让人感到困惑。不过不用担心，因为下一个例子将对其进行更多的探讨。同时，图2.9应该会有所帮助。

<img src="https://blog-andy0570-1256077835.cos.ap-shanghai.myqcloud.com/site_Images/Jietu20200828-151420.png" alt="图 2.9" style="zoom:80%;" />



在上一次练习中的自定义子类中，你将 `UILabel` 子视图添加到 `self.contentView` 中，而不是 `self` 中。一般来说，你不应该直接将子视图添加到集合视图单元中。这就是为什么你应该总是将它们添加到其`contentView` 中。

`UICollectionViewCell` 有三个子视图，在 图2.10 中表示。后面的黑色矩形是集合视图单元格本身，前面的绿色视图是 `contentView`，你可以在那里添加子视图。中间的两个视图是 `selectedBackgroundView` 和 `backgroundView`。这两个视图都是可选的，可以在任何时候设置。`backgroundView` 如果设置了，就会永久存在。

<img src="https://blog-andy0570-1256077835.cos.ap-shanghai.myqcloud.com/site_Images/Jietu20200828-151723.png" alt="图 2.10" style="zoom:80%;" />



现在你对 `UICollectionViewCell` 中的视图层次结构有了更好的理解，你可以继续看另一个例子，它有助于说明这些属性、`contentView` 和图像的用途。

您将创建一个应用程序，在 10 个不同的 section 区域中重复显示 12 张图片，每个部分都将有自己的背景颜色，除非它被选中，演示如何使用 `selectedBackgroundView`。你使用 12 张图片是因为它们正好可以在一个 section 中充满屏幕显示。

基于 Empty 模板创建一个新的 Xcode 项目。创建一个`UICollectionViewController` 的子类和一个 `UICollectionViewCell` 的子类，就像上次一样。在应用程序委托中设置一个视图控制器的实例作为窗口的根视图控制器--这次不需要使用导航控制器。

使用两个数组来存储你的模型：一个用于图像，一个用于存储背景色。你将调整上一个例子中的单元格大小、单元格之间的间距和行间距。此外，你将在集合视图上启用多重选择功能；这将使用户能够同时选择多个单元格，也使用户能够通过点击它们来取消选择单元格。以下示例代码中， `viewDidLoad` 方法中的所有内容应该看起来很熟悉。我创建了一系列JPEG 格式的图片，命名为 0.jpg 到 11.jpg，共12张。

```objc
static NSString *CellIdentifier = @"Cell Identifier";

@implementation AFViewController
{
    // models
    NSArray *imageArray;
    NSArray *colorArray;
}

- (void)viewDidLoad
{
    [super viewDidLoad];
    
    // Set up our models
    NSMutableArray *mutableImageArray = [NSMutableArray arrayWithCapacity:12];
    for (NSInteger i = 0; i < 12; i++)
    {
        NSString *imageName = [NSString stringWithFormat:@"%ld.jpg", (long)i];
        [mutableImageArray addObject:[UIImage imageNamed:imageName]];
    }
    imageArray = [NSArray arrayWithArray:mutableImageArray];
    
    NSMutableArray *mutableColorArray = [NSMutableArray arrayWithCapacity:10];
    for (NSInteger i = 0; i < 10; i++)
    {
        CGFloat redValue = (arc4random() % 255) / 255.0f;
        CGFloat blueValue = (arc4random() % 255) / 255.0f;
        CGFloat greenValue = (arc4random() % 255) / 255.0f;
        
        [mutableColorArray addObject:[UIColor colorWithRed:redValue green:greenValue blue:blueValue alpha:1.0f]];
    }
    colorArray = [NSArray arrayWithArray:mutableColorArray];
    
    // configure our collection view layout
    UICollectionViewFlowLayout *flowLayout = (UICollectionViewFlowLayout *)self.collectionView.collectionViewLayout;
    flowLayout.minimumInteritemSpacing = 20.0f;
    flowLayout.minimumLineSpacing = 20.0f;
    flowLayout.sectionInset = UIEdgeInsetsMake(10, 10, 10, 10);
    flowLayout.itemSize = CGSizeMake(220, 220);
    
    // configure our collection view
    [self.collectionView registerClass:[AFCollectionViewCell class] forCellWithReuseIdentifier:CellIdentifier];
    self.collectionView.indicatorStyle = UIScrollViewIndicatorStyleWhite;
    self.collectionView.allowsMultipleSelection = YES;
    self.collectionView.canCancelContentTouches = NO;
    self.collectionView.delaysContentTouches = NO;  
}
```

因为你要显示多个 section 组，所以你需要实现一个新的、可选的`UICollectionViewDelegate` 方法，称为`numberOfSectionsInCollectionView:`。以下示例代码中所示的`collectionView:cellForItemAtIndexPath:` 实现也看起来与之前很熟悉。

```objc
#pragma mark - UICollectionViewDataSource Methods

-(NSInteger)numberOfSectionsInCollectionView:(UICollectionView *)collectionView
{
    return colorArray.count;
}

-(NSInteger)collectionView:(UICollectionView *)collectionView numberOfItemsInSection:(NSInteger)section
{
    return imageArray.count;
}

-(UICollectionViewCell *)collectionView:(UICollectionView *)collectionView cellForItemAtIndexPath:(NSIndexPath *)indexPath
{
    AFCollectionViewCell *cell = (AFCollectionViewCell *)[collectionView dequeueReusableCellWithReuseIdentifier:CellIdentifier forIndexPath:indexPath];
    
    cell.image = imageArray[indexPath.item];
    cell.backgroundColor = colorArray[indexPath.section];
    
    return cell;
}
```

打开集合视图单元格子类，在该类中添加一个 `UIImageView` 实例变量。同时添加一个名为 `image` 的 `UIImage` 属性。写一个新的初始化器来实例化实例变量。

```objc
@implementation AFCollectionViewCell
{
    UIImageView *imageView;
}

- (id)initWithFrame:(CGRect)frame
{
    if (!(self = [super initWithFrame:frame])) return nil;
    
    self.backgroundColor = [UIColor whiteColor];
    
    imageView = [[UIImageView alloc] initWithFrame:CGRectInset(self.bounds, 10, 10)];
    [self.contentView addSubview:imageView];
    
    UIView *selectedBackgroundView = [[UIView alloc] initWithFrame:CGRectZero];
    selectedBackgroundView.backgroundColor = [UIColor colorWithWhite:1.0f alpha:0.8f];
    self.selectedBackgroundView = selectedBackgroundView;
    
    return self;
}
```

你在图像视图的框架中移动了 10 个点，在图像周围创建一个边框。覆盖 `setImage:` 方法来设置 `imageView` 的图像。你还要重写`prepareForReuse` 方法，并包含一个 `setHighlighted` 的实现。还请注意，你已经将选定的  `BackgroundView `设置为一个纯白色的视图。当单元格被选中时，这个白色视图将被放置在单元格的前面（以及任何背景视图的前面，在本例中没有）。

```objc
#pragma mark - Overriden UICollectionViewCell methods

-(void)prepareForReuse {
    [super prepareForReuse];
    
    self.backgroundColor = [UIColor whiteColor];
    self.image = nil; // also resets imageView’s image
}

-(void)setHighlighted:(BOOL)highlighted {
    [super setHighlighted:highlighted];
    
    if (self.highlighted) {
        imageView.alpha = 0.8f;
    } else {
        imageView.alpha = 1.0f;
    }
}

#pragma mark - Overridden Properties

-(void)setImage:(UIImage *)image {
    _image = image;
    
    imageView.image = image;
}
```

记住在覆盖属性的实现方法时，总是调用 `super` 关键字（除非你故意不想调用，并且有一个非常好的理由）。在实现中，当图像被高亮时，你将图像视图的 `alpha` 降低到 80%。运行应用程序。

玩转应用程序。点击单元格，使它们被选中，然后再点击它们。请注意，如果你点击并拖动，集合视图会取消你的点击并滚动。这是因为 `UIScrollView` 属性 `canCancelContentTouches` 被设置为 `YES`。另外，请注意集合视图如何延迟高亮显示单元格，直到你按住触摸几十分之一秒。这是因为 `UIScrollView` 属性`delaysContentTouches` 被设置为 `YES`。在 `viewDidLoad` 的实现中，可以玩玩这两个方法来试验它们如何影响集合视图的用户体验（事实上，所有滚动视图都是如此，因为这些都是默认值）。

注意关于 `UICollectionViewCell` 的 `selectedBackgroundView` 和 `backgroundView` 属性的几件事。首先，它们将被拉伸以适应它们被分配的任何单元格。这就是为什么在这个例子中，你能够用一个 `CGRectZero` 的边框来初始化选定的背景视图。接下来，一些属性，如 `alpha` 属性，将被集合视图重置为默认值（在 `alpha`的示例中，为 1.0f）。在排除单元格背景的显示问题时要注意这些问题。

如果你想证明 `selectedBackgroundView` 被放置在视图层次结构中，您可以将其设置为稍微透明的颜色。将 `selectedBackgroundView` 的背景色改为类似 `[UIColor colorWithWhite:1.0f alpha:0.8f]` 的颜色。现在你将能够通过 `selectedBackgroundView` 看到它的上层视图，即集合视图本身。

在本章结束关于性能的案例研究之前，我想做一个快速的转移，**重新审视一下 storyboard 和 .xib 文件**。现在你已经了解了集合视图单元的工作原理，并且你可以创建子类来定制它们的外观，看看如何使用故事板和 .xibs 来处理前面的练习。

使用 .xibs 与代码最相似，所以先从这个开始。打开上一次练习中的 Xcode 项目（如果你没有使用源码控制，先复制它），然后添加一个新文件。

在新建文件对话框的左侧窗格下，选择 **User Interface**，然后双击 "**Empty**" 以创建一个新的、空的 `.xib`。给它起一个与你的集合视图单元格子类相同的名字。打开该 `.xib` 文件，在对象库中，找到 "**Collection View Cell**" 并将其拖到空画布上。

选择新的单元格，打开"Size Inspector"，将尺寸设置为 220 宽和 220高。反正这些都会被集合视图重新配置，所以这里设置只是为了帮助我们直观地了解单元格的样子。打开 "Attributes Inspector"，将背景色设为白色。打开 "Identity Inspector"，将 “Custom Class” 类型，即集合视图单元格的类型设置为你的子类。

在子类中，你需要删除很多代码。`initWithFrame:` 初始化器将不再被调用。创建一个名为 `awakeFromNib` 的新方法。当一个类的实例从 nib 中 "解冻 "时，就会调用这个方法。在这个方法中，你放置了你的自定义`selectedBackgroundView` 初始化。看到有些事情无论如何都需要用代码来完成吗？

在 `.xib` 文件中，将 `UIImageView` 对象拖到单元格上。设置 springs 和 struts(或 Autolayout 约束)，使图像视图四面嵌入 10 点。将实例变量移动到头文件中，并将其前缀为关键字 IBOutlet，以便 .xib 可以看到它。命令单击并从集合视图单元格拖动到其内部的图像视图；从出现的菜单中选择图像视图出口。

最后，你需要告诉集合视图使用这个 nib，而不是自己初始化集合视图单元格子类本身的副本。在 `viewDidLoad`中，改变集合视图的设置，如以下示例代码所示。

```objc
- (void)viewDidLoad
{
    [super viewDidLoad];
    
    // ...略去其他的初始化工作
    
    [self.collectionView registerNib:[UINib nibWithNibName:@"AFCollectionViewCell" bundle:nil] forCellWithReuseIdentifier:CellIdentifier];
}
```

运行应用程序，看看它的行为是否和代码一样。注意，即使你使用 `UINib`，你仍然被迫使用子类实现文件。

最后，你要使用故事板来产生同样的效果。清空 `applicationDidFinishLaunchingWithOptions:` 实现，只返回 YES。删除 `.xib` 文件。将 `viewDidLoad` 实现缩减为以下示例代码的样子。

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    
    // 初始化模型,创建并加载图片数据
    NSMutableArray *mutableImageArray = [NSMutableArray arrayWithCapacity:12];
    for (NSInteger i = 0; i < 12; i++) {
        NSString *imageName = [NSString stringWithFormat:@"%ld.jpg",(long)i];
        [mutableImageArray addObject:[UIImage imageNamed:imageName]];
    }
    self.imageArray = [NSArray arrayWithArray:mutableImageArray];
    
    // 初始化模型，创建并加载颜色数据，作为一组 cell 的背景颜色
    NSMutableArray *mutableColorArray = [NSMutableArray arrayWithCapacity:10];
    for (NSInteger i = 0; i < 10; i++) {
        CGFloat redValue = (arc4random() % 255) / 255.0f;
        CGFloat blueValue = (arc4random() % 255) / 255.0f;
        CGFloat greenValue = (arc4random() % 255) / 255.0f;
        
        [mutableColorArray addObject:[UIColor colorWithRed:redValue green:greenValue blue:blueValue alpha:1.0f]];
    }
    self.colorArray = [NSArray arrayWithArray:mutableColorArray];
    
    // 配置集合视图布局
    UICollectionViewFlowLayout *flowLayout = (UICollectionViewFlowLayout *)self.collectionView.collectionViewLayout;
    flowLayout.minimumInteritemSpacing = 20.0f;
    flowLayout.minimumLineSpacing = 20.0f;
    flowLayout.sectionInset = UIEdgeInsetsMake(10, 10, 10, 10);
    flowLayout.itemSize = CGSizeMake(220, 220);
    
    // 配置集合视图
    self.collectionView.allowsMultipleSelection = YES;
}
```

你的 `viewDidLoad` 现在只设置了模型，并在集合视图上设置了一个不能用故事板的属性检查器设置的属性。

创建一个名为 MainStoryboard 的新故事板文件。打开 Xcode 项目设置并将 MainStoryboard 设置为主故事板。(你没看错，伙计们。)将一个`UICollectionViewController` 拖到空的故事板上，并将其自定义类设置为你的代码所在的类。展开集合视图的视图层次结构，直到你到达集合视图单元。将其自定义类设置为你的 `UICollectionViewCell` 子类，并在属性检查器中将其重用标识符设置为单元格标识符。打开 "尺寸 "检查器，将其设置为宽220×高220。

添加一个图像视图作为单元格的子视图；命令点击并从单元格拖动到图像视图，设置单元格的图像视图出口。将图像视图设置为200宽200高，并设置 springs 和 struts(或 Autolayout 约束)，使其尺寸与单元格一起变化。

最后，单击视图层次结构中的集合视图流布局对象。将 " Min Spacing "和 "Section Insets"设置为你之前在代码中使用的值（见图2.11）。运行应用程序。

使用 storyboards 或 .xib 文件的好处是，你可以直观地布局你的界面。当你与设计师一起工作时，或者当你第一次学习 Cocoa Touch 中的视图层次结构时，这将会有很大的帮助。然而，故事板和 .xib 文件除了它们的视觉性质外，并没有提供很多引人注目的优势。故事板和集合视图有两个问题：集合视图单元（它的重用标识符）和代码之间的紧密耦合，以及当你在运行时在代码中修改故事板的设置时，一些棘手的调试。你不能依赖编译时视觉上看到的东西，因为无论如何，它很可能会在运行时被代码改变。

从这一点出发，我不会再关注 .xib 文件或故事板。你已经看到了它们是如何工作的，所以如果你正在将它们整合到一个使用它们的现有项目中，你将能够应用本书中的技术。即使你仍然习惯于用代码而不是视觉方式来布置界面，我也鼓励你使用 .xi b文件而不是故事板。记住使用自定义的 UICollectionViewCell 子类来保持你的代码松散耦合；你的视图控制器不应该知道单元格的视图层次结构的内部情况。

现在你已经很好地理解了 `UICollectionViewCell` 以及如何向用户显示内容，我们来看看性能。



## 案例研究：评估  UICollectionView 的性能

当你评估任何 iOS 应用的性能时，你必须在真机设备上进行测量。使用真实而具体的设备很重要，但最重要的是不要依赖模拟器。虽然它对很多事情都很有用，比如 `NSZombies`，但模拟器环境拥有一整台 PC 的性能作为动力，然而大多数用户的 iPhone 并不是这样的。

选择一个测试设备可能有点棘手。很明显，像 iPhone 5 这样真正的新东西不会是测试你的应用在紧张时的性能的理想选择。然而，也不要依赖使用最老或最慢的硬件。虽然 iPhone 3GS 只有一个核心，但它的实际表现可以比 iPhone 4 好得多，虽然 iPhone 4 有更多的随机访问内存（RAM）和多核中央处理单元（CPU），但必须向其 Retina 屏幕推出四倍的像素。

除了 iPhone，你还必须考虑 iPhone touch。如果你正在编写一款挑战设备极限的应用，你应该在所有硬件/软件组合上进行测试。然而，许多iOS 开发人员只是单人操作，鞭策一些很酷的应用程序，他们没有数千美元用于测试硬件（或理解哪些愿意沉迷于苹果产品的很重要的人）。如果你没有老旧的 iPhone 可供随时使用，iPod touch 很好用，而且价格便宜。

关于集合视图和其他滚动视图，性能最重要的方面是感知滚动响应。请注意，我说的是感知的响应。你可以通过测量屏幕刷新率来衡量。理想的情况下，这个速度应该是每秒60帧（fps），也就是本机刷新率。这意味着在每次调用主运行循环时，你的应用程序只有16毫秒的时间来完成。这并不是很多时间。这个案例研究将强调低效代码严重影响性能的地方，并告诉你如何重组你的代码以保持精简。同样的代码打开性能问题示例项目。解决方案也在那里，前缀为 "Solved"）。

在进入实际的剖析之前，这里还有一个提示。当你在测量你的应用程序的性能时，CPU 的性能会受到 Instruments 的影响（有点像观察者效应）。为了避免这种情况，请打开 Instruments 中的 Preferences，并选中 Always Use Deferred Mode （一律使用延迟模式）复选框。这将在设备上本地收集数据，直到运行完成后才将数据发送到电脑上。

当你拥有了设备，并通过努力在 Apple 设备上面运行你的应用程序后，将它连接到你的电脑上。确保你的设备是从 Scheme-下拉菜单中选择的。在 Xcode 中，打开 Product 菜单，选择 Profile（Command-I）。这将用 Release 构建设置（如编译器优化）构建你的应用程序，并打开Instruments 模板选择器（见图2.12）。



<img src="https://blog-andy0570-1256077835.cos.ap-shanghai.myqcloud.com/site_Images/Jietu20200828-173719%402x.png" alt="图2.12" style="zoom:80%;" />



记住，根据你使用模拟器还是实际设备，你会得到不同的模板。选择“Core Animation” 模板。这将为您提供屏幕刷新率以及 CPU 使用率，这将告诉您 CPU 在哪里花费了大部分时间执行代码。点击 Profile 并滚动应用程序。使用滚动并注意到响应速度有多糟糕。当你意识到这真的是非常非常糟糕的代码时，点击停止按钮，在仪器中查看结果（见图2.13）。

<img src="https://blog-andy0570-1256077835.cos.ap-shanghai.myqcloud.com/site_Images/%E5%9B%BE%E7%89%87%E6%9D%A5%E8%87%AA%20iOS%20UICollectionView%20The%20Complete%20Guide%202nd%20Edition.2014.%E8%8B%B1%E6%96%87%E7%89%88%EF%BC%8C%E7%AC%AC%2053%20%E9%A1%B5.png" alt="图2.13" style="zoom:80%;" />



糟糕的屏幕刷新率! 峰值只有 37fps，太可怕了。选择 "Time Profiler（时间剖析器）"，打开 "Extended Detail（展开细节）"窗格（见图2.14）

![图2.14](https://blog-andy0570-1256077835.cos.ap-shanghai.myqcloud.com/site_Images/%E5%9B%BE%E7%89%87%E6%9D%A5%E8%87%AA%20iOS%20UICollectionView%20The%20Complete%20Guide%202nd%20Edition.2014.%E8%8B%B1%E6%96%87%E7%89%88%EF%BC%8C%E7%AC%AC%2053%20%E9%A1%B5-1.png)

你可以看到，在主线程上，从网上下载图片的时间是最多的。这绝不是一个好主意! 此外，你没有在任何地方缓存下载的数据。在你的视图控制器中添加一个 `NSCache` 实例来保存你缓存的数据结果。这个类是一个方便的小键/值存储，当内存变低时，它会自动释放内存。在 `loadView `中初始化它（见清单2.18）。

```objc
-(void)configureCell:(AFCollectionViewCell *)cell atIndexPath:(NSIndexPath *)indexPath withURLString:(NSString *)urlString
{
    // 尝试从缓存中调出一个 NSData 的缓存实例。
    id data = [photoDataCache objectForKey:urlString];
    
    if (data) {
        // 如果 objectForKey:是非 nil，也就是说我们之前下载了图片，这个分支就会执行。
        if ([data isKindOfClass:[NSNull class]]) {
            // 这表明该实例是NSNull，所以我们不应该使用它。
            
            //nop
        } else {
            // 我们可以成功解压我们的JPEG数据
            dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
                [data af_decompressedImageFromJPEGDataWithCallback:^(UIImage *decompressedImage) {
                    [cell setImage:decompressedImage];
                }];
            });
        }
    } else {
        // 在后台队列中下载图片
        dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
            NSData *data = [self downloadImageDataWithURLString:urlString];
            
            //Now that we have the data, dispatch back to the main queue
            //to use it. UIImage is part of UIKit and can *only* be accessed on
            //the main thread
            dispatch_async(dispatch_get_main_queue(), ^{
                
                UIImage *image = [UIImage imageWithData:data];
                
                if (image) {
                    // 这个作为参数传入的单元格实例现在可能已经被重用了。
                    // 调用 reloadItemsAtIndexPaths: 代替。
                    [photoDataCache setObject:data forKey:urlString];
                    [photoCollectionView reloadItemsAtIndexPaths:@[indexPath]];
                } else {
                    // 这表明 JPEG 解压失败。在我们的缓存中设置NSNull
                    [photoDataCache setObject:[NSNull null] forKey:urlString];
                }
            });
        });
    }
}
```

这段代码非常直观。注意你在方法签名中添加了一个新的参数，一个索引路径。这是用来在该索引路径处重载项目的；直接引用单元格是不安全的，因为它可能已经被重用了。比如说，如果单元格已经被删除，直接重载项目就会遇到问题。这个简单的例子适合本章的需要。如果你要做更复杂的事情，我建议依靠获取结果控制器来更新集合视图。

> 注：
>
> 有更好的方法来缓存照片，比如 Core Data。也有更好的方法从互联网上下载数据，但这个案例研究的重点是研究收集视图性能的问题，而不是一般的软件架构。

用修改后的代码重新运行剖析器。你可以看到，性能有了显著的提高。这很好，但仔细看看你是否还能进一步改进。如果你看一下扩展细节窗格，占用时间最多的方法还是 `configureCell:atIndexPath:withURLString:`。看来 `imageWithData:` 占用了大量的CPU时间。

你可以采取一些方法来处理这个问题。你可以缓存解压后的JPEG文件，这是一个好主意，但它有其缺点。最大的问题是，它消耗了大量的内存。你的图像是145×145 像素，有 3 个通道，一个通道 8 位。这意味着每张图片解压后，要占用145*145*3=63KB。这听起来并不是很多，但应用程序是在内存受限的设备上运行的，如果它使用了太多的内存，操作系统会杀死应用程序。

而是在后台队列上解压 JPEG 数据。"哈！"你说，"`UIImage` 是 UIKit 框架的一部分，叫我在后台队列上使用它是痴人说梦！" 你没的说错，但存在一个替代方案。`UIImage` 是一个方便的类，但在告诉我们它是否已经解压了图像方面是相当不透明的。例如，使用 Core Graphics 框架来解压图像（见清单2.19）。

```objc
typedef void (^JPEGWasDecompressedCallback)(UIImage *decompressedImage);

//Just a utility class to round numbers up
int roundUp(int numToRound, int multiple)
{
	if(multiple == 0)
	{
		return numToRound;
	}
	
	int remainder = numToRound % multiple;
	if (remainder == 0)
		return numToRound;
	return numToRound + multiple - remainder;
}

@implementation NSData (AFDecompression)

-(void)af_decompressedImageFromJPEGDataWithCallback:(JPEGWasDecompressedCallback)callback
{
    uint8_t character;
    [self getBytes:&character length:1];
    
    if (character != 0xFF)
    {
        //This is not a valid JPEG.
        
        callback(nil);
        
        return;
    }
    
    // get a data provider referencing the relevant file
    CGDataProviderRef dataProvider = CGDataProviderCreateWithCFData((__bridge CFDataRef)self);
    
    // use the data provider to get a CGImage; release the data provider
    CGImageRef image = CGImageCreateWithJPEGDataProvider(dataProvider, NULL, NO, kCGRenderingIntentDefault);
    CGDataProviderRelease(dataProvider);
    
    // make a bitmap context of a suitable size to draw to, forcing decode
    size_t width = CGImageGetWidth(image);
    size_t height = CGImageGetHeight(image);
	size_t bytesPerRow = roundUp(width * 4, 16);
	size_t byteCount = roundUp(height * bytesPerRow, 16);
	
	void *imageBuffer = malloc(byteCount);
    
    if (width == 0 || height == 0)
    {
        dispatch_async(dispatch_get_main_queue(), ^{
            callback(nil);
        });
    }
    
    CGColorSpaceRef colourSpace = CGColorSpaceCreateDeviceRGB();
    
    CGContextRef imageContext =
    CGBitmapContextCreate(imageBuffer, width, height, 8, bytesPerRow, colourSpace,
						  kCGImageAlphaNone | kCGImageAlphaNoneSkipLast); //Depsite what the docs say these are not the same thing
    
    CGColorSpaceRelease(colourSpace);
    
    // draw the image to the context, release it
    CGContextDrawImage(imageContext, CGRectMake(0, 0, width, height), image);
    CGImageRelease(image);
    
    // now get an image ref from the context
    CGImageRef outputImage = CGBitmapContextCreateImage(imageContext);
    
    CGContextRelease(imageContext);
    free(imageBuffer);
    
    dispatch_async(dispatch_get_main_queue(), ^{
        UIImage *decompressedImage = [UIImage imageWithCGImage:outputImage];
        callback(decompressedImage);
        CGImageRelease(outputImage);
    });
}
```

这个范畴类是非常有用的。在后台队列上调用解压方法，一切都会为你处理好。`NSData` 实例被解压，如果它实际上是一个 JPEG，就在该方法被调用的队列上。当解压完成后，它会调用一个回调块，并负责清理 `CGImageRef` 内存。

现在你可以在后台队列中安全地解压 JPEG，将其纳入到你的代码中，如清单2.20所示。

```objc
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    [data af_decompressedImageFromJPEGDataWithCallback:^(UIImage *decompressedImage) {
        [cell setImage:decompressedImage];
    }];
});
```

因为JPEG解压只需要几毫秒，所以我在清单2.20中直接更新单元格。如果你要解压的JPEG文件是兆字节大的，这对你来说是行不通的，但是在集合视图中显示这么大的图像，一般来说是个坏主意。

如果你重新运行 Instruments，你会发现，总体来说，最昂贵的操作，是从 `UICollectionViewCell` 的 `initWithFrame:` 中分配空间。这真的很好。从轶事上看，应用程序运行得更流畅了。

击掌! 但是看看代码库中还有两个地方可以改进。

图片是145×145像素，但你的单元格是145×100逻辑像素。`UIImageView` 是把图片缩小到合适的位置。你可以改变 content mode，将它们居中，而不是让它们不被缩放。

理想情况下，你的图像大小和单元格大小应该是一样的，这样操作系统就不需要调整任何大小，从而提高性能。然而，如果你使用的是第三方API，你将无法控制图像大小。

我可以推荐的唯一一件事是打开单元格层的 `masksToBounds` 属性来提高这个例子的性能；你将这个属性与 `cornerRadius` 一起使用。这对CPU造成了压力，因为它可能会产生离屏渲染，可能会导致很多问题。如果你搞不清楚为什么你的集合视图速度很慢，可以检查一下。

如果集合视图背景不透明，你可以在 `contentView` 的 `UIImageView` 子视图中使用PNG 来遮挡角落。这也是一个很好的方法，但是如果可以避免的话，不要使用可调整大小的图片。如果你的所有单元格都是一样大小，就使用不可调整大小的 `UIImage` 来遮挡角落，因为它的渲染速度更快。

第一个性能示例就到此为止。看看下一个例子，叫做 "性能问题实例二"，在相同的代码中。构建并运行该应用，以了解它的工作原理。

这个应用程序的用例如下。你受雇于一家刚刚获得天使资金的新兴创业公司，他们正在建立一个猫咪的社交网络（见图2.15）。你要为他们未来的移动应用做一个相当于 "Facebook墙 "的原型，这样他们就可以抢到数百万的风险投资资金。

<img src="https://blog-andy0570-1256077835.cos.ap-shanghai.myqcloud.com/site_Images/IMG_4701.PNG" alt="图2.15" style="zoom:50%;" />



该应用程序在具有不同背景颜色的单元格中显示注释。模型是在 `setupModel` 中设置的。只需忽略这个方法；它与本案例研究无关。另外，请注意 Xcode 如何让你在 Objective-C 源代码中使用 emoji。这有多酷？

配置应用程序，并使用与上一个例子相同的 Core Animation 模板。峰值帧率为48fps，这并不可怕，但并不理想。当你打开 "扩展细节 "窗格时，你看到的应该会引起一些警觉（见图2.16）。

![图2.16](https://blog-andy0570-1256077835.cos.ap-shanghai.myqcloud.com/site_Images/%E5%9B%BE%E7%89%87%E6%9D%A5%E8%87%AA%20iOS%20UICollectionView%20The%20Complete%20Guide%202nd%20Edition.2014.%E8%8B%B1%E6%96%87%E7%89%88%EF%BC%8C%E7%AC%AC%2060%20%E9%A1%B5.png)



最昂贵的操作性能是卸载 .xib 文件。那是怎么回事？打开 AFCollectionViewCell.xib，沉浸在视图层次结构的纯粹存在感的恐怖中。

很明显，图2.17代表了一个教学实例。你永远不会在一个nib中拥有一个相当糟糕的视图层次结构。即使你有一个相当复杂的视图层次结构，你真正要做的就是在彩色背景上显示一些文本。你可以在 `drawRect:` 中更快地绘制这个视图，以及相当于所有这些无用的视图。你可以在你自己的单元格子类中应用同样的逻辑；如果你有一个复杂的视图层次结构，需要花费太长的时间来绘制，就实现 `drawRect:` 并抛弃视图层次结构。 `drawRect:`也可以是一个性能缓慢的东西，然而，在这样一个简单的例子中使用它只是为了说明它是如何完成的。只有当手动绘制视图的组件比渲染一个必然复杂的视图层次结构更快时，你才应该使用它。

删除单元格头文件中的.xib和两个属性。不在视图控制器的 `viewDidLoad` 中注册一个 UINib，而是注册一个Class。不为颜色使用单独的背景视图，而只是在 `drawRect:` 中绘制它。为单元格的文本创建一个新的字符串属性。你将覆盖 `backgroundColor` 的 `getter` 和 `setter` 来进行一些巧妙的绘制。

```objc
static inline void addRoundedRectToPath(CGContextRef context, CGRect rect, float ovalWidth, float ovalHeight)
{
    float fw, fh;
    if (ovalWidth == 0 || ovalHeight == 0) {
        CGContextAddRect(context, rect);
        return;
    }
    CGContextSaveGState(context);
    CGContextTranslateCTM (context, CGRectGetMinX(rect), CGRectGetMinY(rect));
    CGContextScaleCTM (context, ovalWidth, ovalHeight);
    fw = CGRectGetWidth (rect) / ovalWidth;
    fh = CGRectGetHeight (rect) / ovalHeight;
    CGContextMoveToPoint(context, fw, fh/2);
    CGContextAddArcToPoint(context, fw, fh, fw/2, fh, 1);
    CGContextAddArcToPoint(context, 0, fh, 0, fh/2, 1);
    CGContextAddArcToPoint(context, 0, 0, fw/2, 0, 1);
    CGContextAddArcToPoint(context, fw, 0, fw, fh/2, 1);
    CGContextClosePath(context);
    CGContextRestoreGState(context);
}

@implementation AFCollectionViewCell
{
    UIColor *realBackgroundColor;
}

-(id)initWithFrame:(CGRect)frame
{
    if (!(self = [super initWithFrame:frame])) return nil;
    
    self.opaque = NO;
    self.backgroundColor = [UIColor clearColor];
    
    return self;
}

-(void)drawRect:(CGRect)rect
{
    CGContextRef context = UIGraphicsGetCurrentContext();
    
    CGContextSaveGState(context);
    
    [realBackgroundColor set];

    addRoundedRectToPath(context, self.bounds, 10, 10);
    CGContextClip(context);

    CGContextFillRect(context, self.bounds);
    
    CGContextRestoreGState(context);
    
    [[UIColor whiteColor] set];
    
    [self.text drawInRect:CGRectInset(self.bounds, 10, 10) withFont:[UIFont boldSystemFontOfSize:20] lineBreakMode:NSLineBreakByWordWrapping alignment:NSTextAlignmentCenter];
}

#pragma mark - Overridden Properties

-(void)setBackgroundColor:(UIColor *)backgroundColor
{
    [super setBackgroundColor:[UIColor clearColor]];
    
    realBackgroundColor = backgroundColor;
    
    [self setNeedsDisplay];
}

-(UIColor *)backgroundColor
{
    return realBackgroundColor;
}

-(void)setText:(NSString *)text
{
    _text = [text copy];
    
    [self setNeedsDisplay];
}
```

> 优化原理：通过重写 `drawRect:` 方法通过 Core Graphics 框架绘制视图。

`addRoundedRectToPath` 的 C 方法很方便，可以很容易地修改成只对某些角进行圆角处理。这些方法通常应该放在一个单独的源文件中，以便可以重复使用。请看清单2.22。

```objc
-(void)configureCell:(AFCollectionViewCell *)cell withModel:(AFModel *)model
{
    cell.backgroundColor = [model.color colorWithAlphaComponent:0.6f];
    cell.text = model.comment;
}
```

清单2.22是一个高效的实现。绘图代码很直接，使用单元格的代码在MVC 架构中也能很好地工作。重新编译应用程序。图2.18 在Instruments中第一次剖析运行情况

<img src="https://blog-andy0570-1256077835.cos.ap-shanghai.myqcloud.com/site_Images/%E5%9B%BE%E7%89%87%E6%9D%A5%E8%87%AA%20iOS%20UICollectionView%20The%20Complete%20Guide%202nd%20Edition.2014.%E8%8B%B1%E6%96%87%E7%89%88%EF%BC%8C%E7%AC%AC%2064%20%E9%A1%B5.png" alt="图 2.18" style="zoom:80%;" />

图2.18显示，当你去掉一个单元格的时候，有一个叫做 performLongRunningTask 的方法被调用。它严重阻碍了帧刷新率。你不应该在主线程上执行长运行任务，而且如果你看代码，它是由 prepareForReuse 调用的。开发者把为重用做准备和已经向用户展示了单元格混为一谈。这确实是不可接受的。将这个逻辑重构到视图控制器中（见清单2.23）。

> 有个小技巧。我在 `performLongRunningTask` 方法中设置了一个空的 for 循环，故意造成性能问题，但为了让这个例子正常工作，我不得不禁用编译器优化。LLVM 太聪明了，如果启用了编译器优化，它就会把空循环剥离出来。

```objc
#pragma mark - UICollectionViewDataSource & UICollectionViewDelegate Methods

-(void)collectionView:(UICollectionView *)collectionView didEndDisplayingCell:(UICollectionViewCell *)cell forItemAtIndexPath:(NSIndexPath *)indexPath
{
    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
        [self performLongRunningTask];
    });
}

-(void)performLongRunningTask
{
    /*
     Let's run some long-running task. Maybe it's some complicated view
     hierarchy math that could be simplified with Autolayout.
     */
    for (int i = 0; i < 5000000; i++);
}
```

现在，调用长期运行的任务的代码在适当的地方，任务在后台队列上执行。很好。重新提交应用程序。帧率大约在55fps左右，这是相当不错的。代码中最慢的部分是 `drawRect:`，这会导致一些性能问题。如前所述，**`drawRect:`只有在你有一个必然复杂的视图层次结构时才是一个好的路线**。



