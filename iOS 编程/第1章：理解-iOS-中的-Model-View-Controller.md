> 注：
> 本文翻译自 《iOS UICollectionView The Complete Guide 2nd Edition》
> 使用的翻译工具：<https://www.deepl.com/translator>

在深入学习 `UICollectionView` 之前，你应该先熟悉本书中使用的一些约定和术语。本书从 iOS 应用生命周期的基础知识开始，然后讨论模型-视图-控制器（MVC）模式。即使你是一名经验丰富的 iOS 开发者，已经熟悉了这些主题，我也鼓励你阅读这一章，以确保你在阅读本书其他内容时，与我处于同一页面（或者说，同一视角）。

<!-- more -->

## 应用生命周期基础

iOS 应用的生命周期与其他平台上典型的原生应用有一点不同（尽管最近对 OS X 的改变表明苹果有意将 iOS 的生命周期作为标准）。开发者不再有硬性的规则被参考以衡量他们的应用何时被终止、暂停等等。让我们从一个简单的场景开始，来描述一个典型的应用生命周期。

假设用户刚刚打开手机时，除了属于操作系统的应用外，没有任何应用在运行。你的应用也没有运行。当用户点击你的应用图标后，Springboard（操作系统中操作 iOS 主屏幕的模块）就会启动你的应用。当 Springboard 在屏幕上显示 Default.png 图片时，你的应用程序和它需要执行的共享库被加载到内存中。最终，你的应用程序开始执行，你的应用程序委托会收到相应的通知。当你的应用程序正在运行并处于前台时，它处于**活动（active）**状态。

在 iOS 系统中，用户往往只使用任何特定的应用几秒钟后就会把手机放回口袋。当用户按下 iPhone 或 iPad 上的 Home 键收起应用后，应用就会进入**后台（background）**状态。通常情况下，此时应用程序有 10 秒钟的时间来完成任何数据库保存或其他长期运行的任务（尽管应用程序可以向操作系统申请额外的时间）。当所有的后台任务处理完成后，应用程序最终进入**暂停（suspended）**状态。进入暂停状态后，应用程序仍在内存中，但不会执行代码。你的应用程序的状态是持久的。如果用户在应用程序被暂停的时候打开它，它就会从停止的地方开始执行。当操作系统内存不足时，操作系统可以杀死处于暂停状态的应用程序以回收内存。用户也可以从多任务窗口中手动终止你的应用程序。应用程序一旦被终止，就会返回到不在运行的**初始（initialized）**状态。

但是，等一下，它也可以变得更复杂！当你的应用正处于**活动（active）**状态时，此时如果用户收到日历弹窗提醒，打开多任务窗口，或者接到电话，你的应用程序就会进入**非活动（inactive）**状态。你的应用程序仍然在运行，但它不再是用户交互的首要事物。例如，游戏会自动暂停。作为一名应用开发者，你需要意识到这一点，并将其作为用户可能很快离开你的应用程序的指示。

用户可以无需在主屏幕上点击应用图标就打开你的应用程序。如果你的应用程序可以接收本地通知或推送通知，或者如果它注册了自定义 URL Schema 处理事件，用户可以以任何方式打开它。

对于任何想要做出内容丰富、有沉浸式体验应用的 iOS 开发者来说，应用的生命周期非常重要。这些类型的应用正是 `UICollectionView` 的伟大之处，所以如果不对应用生命周期进行总结，那么对 `UICollectionView` 的全面讨论就不会完整。

如果你的应用进入**非活动（inactive）**状态，请停止更新你的界面。如果用户看到你的集合视图的内容在移动，而他正在决定是否查看已经弹出在你的应用程序上方的会议日程细节，这将会让他感到不安。同样，不要在应用处于**后台（background）**状态时更新你的应用界面。在从活动状态到后台状态再到活动状态的切换之间，用户界面的状态应该保持固定。

## 如何使用 MVC

MVC 并不是一个很难理解的概念，但强调它在 iOS 中的重要性主要有两个原因： 

* MVC 被 Cocoa Touch（以及 OS X 上的 Cocoa）使用。如果你遵守同样的规范，并作为用于编写所有 iOS 应用程序的框架，你的代码会很流畅，而且不会与内置类（包括 `UICollectionView`）发生冲突。

* MVC 总体上是一个很好的设计模式，使用它可以帮助你写出编码规范、易于维护的应用程序。

既然知道了为什么 MVC 很重要，那就来看看什么是 MVC 吧。图1.1 显示了 MVC 的基本情况，强关联用实线表示，弱关联用虚线表示。强关联和弱关联向编译器指明了如何管理内存，对于避免内存泄漏，以免导致应用程序最终因异常而终止非常重要。

![图1.1](https://upload-images.jianshu.io/upload_images/2648731-154fc21b8b024241.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/800)


MVC 的核心是控制器对象。控制器是一个视图控制器，就像 `UIViewController`  一样，它控制着这个视图。它与这个视图保持着紧密的关系，也就是在屏幕上呈现给用户的东西。控制器还与模型保持着紧密的关系。模型表示的是视图中的数据。

如果你的视图曾经对你的模型有引用，或者反之，你就做错了。本书中的示例遵循 MVC 设计模式，你也应该如此。

任何给定应用程序中的大部分代码都写在控制器中；控制器协调并处理视图和模型之间的交互，这就是为什么控制器中的代码通常被称为**胶水代码（glue code）**。

控制器会处理什么样的交互呢？好吧，如果视图包含一个按钮，当用户点击该按钮时，视图控制器会得到通知。用户交互通常会触发修改、创建或删除属于控制器的模型的操作。控制器从视图中接收用户交互，更新模型，然后更新视图以反映对模型的更改。

有时，模型会在没有用户交互的情况下发生变化。例如，考虑一个显示大 JPEG 的视图，该视图正在下载。当下载完成时，应该通知控制器，以便它可以更新视图。在 iOS 上，你有几种不同的选择来通知控制器。我最喜欢的是键值观察者（KVO）。控制器可以将自己注册为模型对象的观察者，这样每当模型的属性发生变化时，就会通知他们。iOS 上模型与控制器交互的其他方式包括 `NSNotificationCenter`、委托和 `NSFetchedResultsController` 。我会避免使用 `NSNotificationCenter` 来进行模型控制器交互，而选择 `NSFetchedResultsController` 或 KVO。虽然本书没有讨论 Core Data，但 `UICollectionView` 与`NSFetchedResultsController` 的工作原理与 `UITableViewController` 类似，非常好用。

最后一个例子展示了 MVC 中的一个漏洞：网络代码去哪里了？作为一个负责任的 iOS 开发者，你应该让视图控制器只负责调解视图和模型之间的交互。如果是这样，就不应该用它来存放网络访问代码。正如在第 6 章 "为 `UICollectionView` 添加交互性 "中所讨论的那样，网络代码应该放在典型的 MVC 金字塔之外。网络访问无论如何都不应该涉及到视图，但它有时会涉及到模型。

嗯，大部分情况下是这样的。事实上，从应用程序编程接口（API）中获取模型细节的常见范式涉及到 Grand Central Dispatch 中的 Block 块。Block 块可以让开发人员把匿名函数当作一级 Objective-C 对象。这些 Block 块可以在以后被调用。控制器可以启动一个网络请求，并向网络获取对象传递一个更新视图的回调 Block 块。从技术上讲，网络代码有一个对视图的间接引用，但请你忽略它，以免你发现自己掉进了一个迂腐的兔子洞中。

如果你有 iOS 开发的经验，这一切听起来应该很熟悉。`UICollectionView` 和 `UICollectionViewController` 并不是以孤岛的形式存在的，它们在应用程序中与模型以及 Cocoa Touch 中的其他部分一起使用。在 MVC 之外的任何其他上下文中呈现它们都是不负责任的。

## MVC 和 UICollectionView

现在你已经阅读了 MVC 设计模式，接下来看看它在编写 `UICollectionView` 代码时该如何使用。

使用 `UICollectionView` 的 MVC 的视图组件不出意外就是 `UICollectionView` 本身，控制器是 `UICollectionViewController` 的子类，或者是 `UIViewController` 的子类，它需要遵守 `UICollectionViewDataSource` 和 `UICollectionViewDelegate` 协议，模型可以是任何东西。

和 `UITableView` 一样，你的控制器可以是 `UIViewController` 子类，并遵守集合视图数据源和委托的两个协议，也可以是 `UICollectionViewController` 子类。如果你看一下 `UICollectionViewController` 的头文件，你会发现它的内容非常少。控制器继承自 `UIViewController` ——遵守 `UICollectionViewDataSource` 和`UICollectionViewDelegate` 协议——并有一个便捷初始化方法来使用具有特定布局的集合视图以编程方式创建它的实例。它包含一个用于访问集合视图的属性和另一个用于指定集合视图的选中状态是否在它（重新）出现时被清除的属性。

当使用 `UICollectionViewController` 子类时，`UIViewController` 的 `view` 属性指向与 `UICollectionViewController` 的 `collectionView` 属性相同的对象。该视图就是集合视图。如果你打算只用`UICollectionView` 向用户显示数据，我强烈建议你将这个预设的控制器子类化。根据我的经验，使用苹果公司提供的这些特殊控制器，你会遇到更少的 "麻烦"。

在某些情况下，子类化 `UIViewController` 是比较好的。例如，如果你的视图包含一个集合视图，但也包含其他视图，那么将集合视图作为控制器根视图的子视图会更容易。区别虽小，但很重要。

图 1.2 和 1.3 展示了使用集合视图的两种方法的差异。`UICollectionViewControll` 更简单，它应该是你首先采取的方法。如果你发现用它不能解决你的问题，就切换到使用第二种方法。从使用第一种方法切换到第二种方法通常很容易。

![图 1.2](https://upload-images.jianshu.io/upload_images/2648731-bc90986ba54af3bd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/800)

![图 1.3](https://upload-images.jianshu.io/upload_images/2648731-60dc2cdb1d13eb41.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/800)

除非有很好的理由不这样做，否则本书会采用第一种方法。尽管 `UICollectionViewController` 的 `view` 属性与其 `collectionView` 属性是一样的，但本书中使用的代码会仔细区分两者。

现在你已经看到了集合视图是如何适应 iOS 应用的 MVC 设计模式的，请看下面的简单例子。别担心，在第2章 "使用 `UICollectionView` 显示内容 "中，你会对集合视图进行很多实验。

在下面的例子中，你创建了一个简单的 iPhone 应用程序，显示一堆具有随机颜色的单元格。要开始，请使用 "Single View template" 模板创建一个新的应用程序。确保 "Use Storyboards "没有被选中；本书的重点是集合视图，我不想非要岔开来讨论 Storyboard 的特殊性。删除视图控制器头文件中的所有内容，并用清单1.1中的代码替换。

```objc
@interface AFViewController : UICollectionViewController

@end
```

用你的视图控制器的名字代替 `AFViewController`。我的首字母是 AF，所以我在我的类名前加上它们，以避免命名空间冲突。

接下来，前往你的 .xib 文件，删除视图。添加一个集合视图（`UICollectionView`）到空白画布上，并将集合视图的 `delegate` 和 `dataSource` 插座变量连接到 File’s Owner 属性，即视图控制器。完成后，它应该像图1.4一样。

![图1.4](https://upload-images.jianshu.io/upload_images/2648731-9c69a69ad2c1e65d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

现在到了有趣的部分：代码部分！集合视图的数据源协议 `UICollectionViewDataSource` 有两个必须遵守的方法。一个是返回每组集合有几个元素，另一个配置指定索引上的元素。

如果你不熟悉这些术语，不要担心。第 2 章将详细解释所有的内容。这个快速的例子只是让你尝尝鲜。

遵循 MVC 设计模式，你需要一个模型。这里我使用一个数组（`NSArray`）保存模型数据，我们一堆随机颜色来代码数据源数据模型。你的实现文件的顶部应该像清单1.2一样。

```objc
#import "AFViewController.h"

static NSString * const reuseIdentifier = @"Cell";

@interface AFViewController ()
@property (nonatomic, copy) NSArray *colorArray;
@end

@implementation AFViewController

- (void)viewDidLoad {
    [super viewDidLoad];
        
    // 注册重用集合视图 Cell
    [self.collectionView registerClass:[UICollectionViewCell class] forCellWithReuseIdentifier:reuseIdentifier];
    
    // 初始化数据源模型，创建 100 个随机颜色
    const NSInteger numberOfColors = 100;
    NSMutableArray *tempArray = [NSMutableArray arrayWithCapacity:numberOfColors];
    for (NSInteger i = 0; i < numberOfColors; i++) {
        CGFloat red = arc4random()/(CGFloat)INT_MAX;
        CGFloat green = arc4random()/(CGFloat)INT_MAX;
        CGFloat blue = arc4random()/(CGFloat)INT_MAX;
        UIColor *randomColor = [UIColor colorWithRed:red green:green blue:blue alpha:1.0];
        [tempArray addObject:randomColor];
    }

    // !!!: 使用 NSMutableArray 可变数组创建数据，赋值到数据源时使用 NSArray 不可变数组，以提高性能。
    _colorArray = [NSArray arrayWithArray:tempArray];
}
```

请注意数组的复制，我们这样做是为了避免用一个可变的实例作为我们的颜色数组，这样做会不必要的慢。

`reuseIdentifier` 字符串用来注册一个普通的 `UICollectionViewCell` ，作为集合视图要使用的元素，所以不用太在意它。涉及到模型的部分是名为 `colorArray` 的属性。在 `viewDidLoad` 方法中，使用 `for` 循环来用随机颜色填充这个数组。

现在你已经设置好了模型，你需要配置你的视图来表示它。为此，使用前面提到的两个 `UICollectionViewDataSource` 方法。

```objc
// 每组集合有几个元素
- (NSInteger)collectionView:(UICollectionView *)collectionView numberOfItemsInSection:(NSInteger)section {
    return _colorArray.count;
}

// 分别配置每个元素
- (UICollectionViewCell *)collectionView:(UICollectionView *)collectionView cellForItemAtIndexPath:(NSIndexPath *)indexPath {
    UICollectionViewCell *cell = [collectionView dequeueReusableCellWithReuseIdentifier:reuseIdentifier forIndexPath:indexPath];
    cell.backgroundColor = _colorArray[indexPath.item];
    return cell;
}
```

第一个方法 `-collectionView:numberOfItemsInSection:`——让集合视图知道要显示多少个单元格。你通过返回模型的数量让控制器知道要返回的单元格数量。

接下来是 `collectionView:cellForItemAtIndexPath:` 方法，它返回一个单元格，你需要负责以一种代表你的模型的方式进行配置。要做到这一点，你在给定的索引处获取模型，并使用该颜色作为单元格的背景色。如果你运行应用程序，你会得到像图 1.5 中看到的东西。因为颜色是随机生成的，当然，你的应用程序看起来会有所不同。

![图 1.5](https://upload-images.jianshu.io/upload_images/2648731-fdc6a1cf5f14e0e5.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)

请注意，我们并没有在 `UINavigationController` 中使用这个集合视图，所以状态栏是透明的。在 iOS 7+ 的代码中，你通常会将你的集合视图封装在一个导航控制器内，其导航栏延伸到状态栏后面。

所以，这个简单的例子演示了一个模型如何表示一个视图，以及如何配置一个视图来表示该模型，而两者都不知道对方。这个例子展示了你应该追求的柏拉图式的理想：模型、视图和控制器之间的明确分离。



## 总结

* 了解应用程序的生命周期：**活动（active）**状态、**非活动（inactive）**状态、**后台（background）**状态、**暂停（suspended）**状态、**初始（initialized）**状态。
* MVC 设计模式概述、MVC 与 `UICollectionView` 集合视图的关系，如何将`UICollectionView` 集合视图应用到 MVC 设计模式中。
* `UICollectionViewController` 的简单 Demo 示例。


