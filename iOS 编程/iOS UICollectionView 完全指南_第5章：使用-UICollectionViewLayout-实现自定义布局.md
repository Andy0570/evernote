> 注：
> 本文翻译自 《iOS UICollectionView The Complete Guide 2nd Edition》
> 使用的翻译工具：<https://www.deepl.com/translator>

在上一章中，我写道，`UICollectionViewFlowLayout` 对于基于行的、分块的布局是非常好的，你应该总是首先使用它。然而，有时候，我们的布局足够复杂，需要使用更强大的东西。`UICollectionViewLayout` 是`UICollectionViewFlowLayout` 的父类，它是一个抽象布局类。因此你要负责一切--包括单元格的布、集合视图的大小。我们将看一看你想在哪里使用它的例子，重新审视装饰视图，并探索一下用动画在布局之间的程序化变化。在本章的最后，我们使用一个 web 服务和一些很酷的自定义布局，构建了一个非常酷的照片应用程序。



## 创建 `UICollectionViewFlowLayout` 的子类

我不想吓唬你不要去直接创建 `UICollectionViewLayout`的子类，但我要重申，这是最后的手段，只有在已经探索了子类 `UICollectionViewFlowLayout` 的选项时才能使用。把它当作对你的警告，以免你发现自己写了很多不必要的代码。

如果你的布局不是基于当它碰到屏幕边缘时就会断裂的线条，直接子类 `UICollectionViewLayout` 可能适合你。如果你发现自己要写代码重现`UICollectionViewFlowLayout` 中的逻辑，请重新考虑直接子类它。

`UICollectionView` 不会为你做任何繁重的工作，你必须自己做所有的事情。让我们看一个比较简单的例子来理解我的意思。

当苹果在 WWDC 2012 上介绍 `UICollectionView` 时，他们有几场会议谈到了这个类和它的布局。遗憾的是，他们提供的示例代码很稀少，而且充满了不准确或简化的地方。我们将以我们自己的方式来看看他们制作的布局之一环状布局。

我们的每一个单元格都将围绕屏幕上的某个点排列成一个圆圈。(我们将在下一章中 "验证 "这一点，以增加交互性；我现在只讨论布局方面的问题。) 每个单元格与该点的距离是相同的。我们还将调整每个单元格的 transform3D，使其 "指向 "圆心。最后，我们将重新审视装饰视图；我们已经有一段时间没有处理它们了，将我们的一些新技术重新应用到它们身上会很有趣。

为了让它变得有趣，我们将在导航栏中添加两个按钮：一个用于添加新的单元格，一个用于删除它们（当然要有动画）。我们还将有一个基本的 `UICollectionViewFlow` 布局来向你展示如何在布局之间进行动画。虽然这应该是非常简单的，但要想正确地工作，往往需要一些巧思。

首先创建一个空的应用程序。在应用程序委托中，创建一个 `UINavigationController` 属性，作为我们窗口的根视图控制器。用我们自己的视图控制器的实例来实例化它。现在大家应该对这个过程很熟悉了。只是别忘了把 QuartzCore 框架添加到你链接的库中。我将`#import <QuartzCore/QuartzCore.h>` 放在我的预编译头中，这样我就不必在每个文件中导入它。参见清单5.1中的基本应用设置。

```objc
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];
    self.viewController = [[UINavigationController alloc] initWithRootViewController:[[AFViewController alloc] init]];
    self.viewController.navigationBar.barStyle = UIBarStyleBlack;
    self.window.rootViewController = self.viewController;
    [self.window makeKeyAndVisible];
    return YES;
}
```

我们的模型会很简单，它只是一个要显示的单元格数量的整数，整数的值会随着单元格的增减而增减。真的很简单。我们将为这个数字创建一个属性，并为我们的两个布局和我们的分段控件创建一个属性（见清单5.2）。

```objc
@interface AFViewController ()

@property (nonatomic, assign) NSInteger cellCount;

@property (nonatomic, strong) AFCollectionViewCircleLayout *circleLayout;
@property (nonatomic, strong) AFCollectionViewFlowLayout *flowLayout;

@property (nonatomic, strong) UISegmentedControl *layoutChangeSegmentedControl;

@end
```

`loadView` 和 viewDidLoad 方法也很简单，它们实例化我们的属性并设置我们的导航项，如清单5.3所示。

```objc
-(void)loadView
{
    // Create our view
    
    // Create instances of our layouts
    self.circleLayout = [[AFCollectionViewCircleLayout alloc] init];
    self.flowLayout = [[AFCollectionViewFlowLayout alloc] init];
    
    // Create a new collection view with our flow layout and set ourself as delegate and data source.
    UICollectionView *collectionView = [[UICollectionView alloc] initWithFrame:CGRectZero collectionViewLayout:self.circleLayout];
    collectionView.dataSource = self;
    collectionView.delegate = self;
    
    // Register our classes so we can use our custom subclassed cell and header
    [collectionView registerClass:[AFCollectionViewCell class] forCellWithReuseIdentifier:CellIdentifier];
    
    // Set up the collection view geometry to cover the whole screen in any orientation and other view properties.
    collectionView.autoresizingMask = UIViewAutoresizingFlexibleWidth | UIViewAutoresizingFlexibleHeight;
    
    // Finally, set our collectionView (since we are a collection view controller, this also sets self.view)
    self.collectionView = collectionView;
    
    // Setup our model
    self.cellCount = 12;
}

- (void)viewDidLoad
{
    [super viewDidLoad];
    
    self.navigationItem.leftBarButtonItem = [[UIBarButtonItem alloc] initWithBarButtonSystemItem:UIBarButtonSystemItemAdd target:self action:@selector(addItem)];
    self.navigationItem.rightBarButtonItem = [[UIBarButtonItem alloc] initWithBarButtonSystemItem:UIBarButtonSystemItemTrash target:self action:@selector(deleteItem)];
    
    self.layoutChangeSegmentedControl = [[UISegmentedControl alloc] initWithItems:@[@"Circle", @"Flow"]];
    self.layoutChangeSegmentedControl.selectedSegmentIndex = 0;
    [self.layoutChangeSegmentedControl addTarget:self action:@selector(layoutChangeSegmentedControlDidChangeValue:) forControlEvents:UIControlEventValueChanged];
    self.navigationItem.titleView = self.layoutChangeSegmentedControl;
}

-(void)layoutChangeSegmentedControlDidChangeValue:(id)sender
{
    // We need to explicitly tell the collection view layout that we want the change animated.
    if (self.collectionView.collectionViewLayout == self.circleLayout)
    {
        [self.flowLayout invalidateLayout];
        [self.collectionView setCollectionViewLayout:self.flowLayout animated:YES];
    } else {
        [self.circleLayout invalidateLayout];
        [self.collectionView setCollectionViewLayout:self.circleLayout animated:YES];
    }
}
```

`layoutChangeSegmentedControlDidChangeValue:` 的实现是非常基本的。我们以后会给它添加更多的内容，用动画来点缀一下。请注意，在将布局交给集合视图使用之前，它先将布局无效化。这一点非常重要。如果我们不这样做，集合视图可能是横向的，但却用纵向计算来布局。我知道这听起来像是系统应该自动会为你处理的事情，但你必须自己去做。我们还需要指明启用 iOS 6 的旋转功能。

```objc
-(BOOL)shouldAutorotate
{
    return YES;
}

-(NSUInteger)supportedInterfaceOrientations
{
    return UIInterfaceOrientationMaskAll;
}
```

流式布局的实现很简单，如清单5.5所示。

```objc
-(id)init {
    if (!(self = [super init])) return nil;
    
    self.itemSize = CGSizeMake(200, 200);
    self.sectionInset = UIEdgeInsetsMake(13.0f, 13.0f, 13.0f, 13.0f);
    self.minimumInteritemSpacing = 13.0f;
    self.minimumLineSpacing = 13.0f;
    
    // Must instantiate these in init or else they'll always be empty
    self.insertedRowSet = [NSMutableSet set];
    self.deletedRowSet = [NSMutableSet set];
    
    return self;
}
```

现在我们已经有了基本的流式布局，让我们来了解这个例子的内涵：圆圈布局（见清单5.6）。创建一个新的类，它是 `UICollectionViewLayout` 的子类。我们将重写 `collectionViewContentSize` 方法，以简单地返回集合视图本身的大小，防止它滚动。我们还将重载 `prepareLayout` 方法来设置我们的圆心和它的半径；我们将在这里抓取集合视图中的单元格数量。

这可能代表了我们应用架构中关注点分离的冲突。毕竟，布局是不是应该不知道它们所帮助显示的数据呢？这倒是真的。然而，在这种情况下，显示的单元格数量会影响布局，所以访问这些信息是合适的。

```objc
-(void)prepareLayout
{
    [super prepareLayout];
    
    CGSize size = self.collectionView.bounds.size;
    
    self.cellCount = [[self collectionView] numberOfItemsInSection:0];
    self.center = CGPointMake(size.width / 2.0, size.height / 2.0);
    self.radius = MIN(size.width, size.height) / 2.5;
}

-(CGSize)collectionViewContentSize
{
    CGRect bounds = [[self collectionView] bounds];
    return bounds.size;
}

-(BOOL)shouldInvalidateLayoutForBoundsChange:(CGRect)newBounds
{
    return YES;
}

- (UICollectionViewLayoutAttributes *)layoutAttributesForItemAtIndexPath:(NSIndexPath *)path
{
    UICollectionViewLayoutAttributes* attributes = [UICollectionViewLayoutAttributes layoutAttributesForCellWithIndexPath:path];
    
    attributes.size = CGSizeMake(kItemDimension, kItemDimension);
    attributes.center = CGPointMake(self.center.x + self.radius * cosf(2 * path.item * M_PI / self.cellCount - M_PI_2), self.center.y + self.radius * sinf(2 * path.item * M_PI / self.cellCount - M_PI_2));
    attributes.transform3D = CATransform3DMakeRotation((2 * M_PI * path.item / self.cellCount), 0, 0, 1);
    
    return attributes;
}

/**
 该方法返回一个包含所有布局信息 UICollectionViewLayoutAttributes 的数组。
 比如我们可以把 CollectionView 中所有 cell 都旋转 45°。
 */
-(NSArray *)layoutAttributesForElementsInRect:(CGRect)rect
{
    NSMutableArray* attributes = [NSMutableArray array];
    
    for (NSInteger i = 0 ; i < self.cellCount; i++)
    {
        NSIndexPath* indexPath = [NSIndexPath indexPathForItem:i inSection:0];
        [attributes addObject:[self layoutAttributesForItemAtIndexPath:indexPath]];
    }
    
    if (CGRectContainsPoint(rect, self.center))
    {
        [attributes addObject:[self layoutAttributesForDecorationViewOfKind:AFCollectionViewFlowDecoration atIndexPath:[NSIndexPath indexPathForItem:0 inSection:0]]];
    }
    
    return attributes;
}
```

`layoutAttributesForItemAtIndexPath:` 可能看起来有点混乱，但它只是一个简单的沿圆的点的公式。我们还旋转每个单元格，使其底边与圆的切线平行。

最后，我们需要创建我们的单元格子类和我们的 `UICollectionViewDataSource` 方法（见清单5.7）。

```objc
@interface AFCollectionViewCell ()

@property (nonatomic, strong) UILabel *label;

@end

@implementation AFCollectionViewCell

- (id)initWithFrame:(CGRect)frame
{
    if (!(self = [super initWithFrame:frame])) return nil;
    
    self.backgroundColor = [UIColor orangeColor];
    
    self.label = [[UILabel alloc] initWithFrame:CGRectMake(0, 0, CGRectGetWidth(frame), CGRectGetHeight(frame))];
    self.label.backgroundColor = [UIColor clearColor];
    self.label.textAlignment = NSTextAlignmentCenter;
    self.label.textColor = [UIColor whiteColor];
    self.label.font = [UIFont boldSystemFontOfSize:24];
    [self.contentView addSubview:self.label];
    
    return self;
}

-(void)prepareForReuse
{
    [super prepareForReuse];
    
    [self setLabelString:@""];
}

-(void)applyLayoutAttributes:(UICollectionViewLayoutAttributes *)layoutAttributes
{
    [super applyLayoutAttributes:layoutAttributes];
 
    self.label.center = CGPointMake(CGRectGetWidth(self.contentView.bounds) / 2.0f, CGRectGetHeight(self.contentView.bounds) / 2.0f);
}

-(void)setLabelString:(NSString *)labelString
{
    self.label.text = labelString;
}
```

单元格只是显示一些文字；我们的两个布局都将使用它来显示单元格项号。applyLayoutAttributes: 实现了设置标签的中心点，这样它将在后面的布局变化动画中被插值。我们不能在这里使用 `frame` 属性，因为那会立即改变标签的边界，而不是通过动画的方式。清单5.8显示了一个基本的集合视图数据源实现。

```objc
- (NSInteger)collectionView:(UICollectionView *)view numberOfItemsInSection:(NSInteger)section;
{
    return self.cellCount;
}

- (UICollectionViewCell *)collectionView:(UICollectionView *)collectionView cellForItemAtIndexPath:(NSIndexPath *)indexPath;
{
    AFCollectionViewCell *cell = (AFCollectionViewCell *)[collectionView dequeueReusableCellWithReuseIdentifier:CellIdentifier forIndexPath:indexPath];
    
    [cell setLabelString:[NSString stringWithFormat:@"%d", indexPath.row]];
    
    return cell;
}
```

让我们运行这个应用程序，看看它是什么样子的。图5.1显示了我们的应用程序的运行情况。

<img src="https://blog-andy0570-1256077835.cos.ap-shanghai.myqcloud.com/site_Images/screenshot_01-1.PNG" alt="图 5.1" style="zoom:40%;" />



还不错！记得，这是一个很简单的布局。记住，这是一个非常简单的布局。它根本没有做任何花哨的事情；它甚至没有滚动。让我们来看看流式布局。请记住，我们并没有在流程布局中做任何特别的事情，它都是内置的（见图5.2）。

<img src="https://blog-andy0570-1256077835.cos.ap-shanghai.myqcloud.com/site_Images/screenshot_02-1.PNG" alt="图 5.2" style="zoom:40%;" />



让我们把它变得更有趣一些。旋转设备，注意到圆圈布局的动画效果不是很好。实际上，你必须切换布局才能让集合视图意识到它的方向已经改变。如果你还记得我们在第4章 "用UICollectionViewFlowLayout组织内容 "中做的Cover Flow布局，我们需要让集合视图布局知道，当集合视图的边界改变时，它应该使自己无效。在圆圈布局中实现下面的方法；它将让集合视图知道，当它的边界发生任何变化时（例如旋转时的变化），布局就会变得无效（见清单5.9）。

```objc
-(BOOL)shouldInvalidateLayoutForBoundsChange:(CGRect)newBounds
{
    return YES;
}
```

这很好。但我知道，我们可以做得更好。



## 以动画方式更新 `UICollectionViewLayout ` 布局

让我们改变我们对 `layoutChangeSegmentedControlDidChangeValue:`  的实现，以显式地对集合视图布局的变化进行动画处理（见清单5.10）。

```objc
-(void)layoutChangeSegmentedControlDidChangeValue:(id)sender
{
    // We need to explicitly tell the collection view layout that we want the change animated.
    if (self.collectionView.collectionViewLayout == self.circleLayout)
    {
        [self.flowLayout invalidateLayout];
        [self.collectionView setCollectionViewLayout:self.flowLayout animated:YES];
    } else {
        [self.circleLayout invalidateLayout];
        [self.collectionView setCollectionViewLayout:self.circleLayout animated:YES];
    }
}
```

你必须使用 `setCollectionViewLayout:animated:` 方法来获取动画，在动画块中设置`collectionViewLayout`属性是不够的。

虽然这个方法仍然会有动画的变化，但在动画过程中，一些单元格会被重复。苹果公司不能决定`collectionViewLayout`是否是一个隐含的可动画属性，这是一个遗憾。

现在我们对布局的变化进行了动画处理，`UICollectionView` 布局将对每个单元格的布局属性变化进行插值。

图5.3显示了集合视图布局变化动画的两个中间阶段。作为开发者，你可以从`UICollectionView`中免费获得这个动画。还不错!

现在我们的布局变化动画已经完成，让我们添加一些花哨的插入和删除动画来配合。我们先做流程布局，因为它比较简单。

记住，我们需要实现 `prepareForCollectionViewUpdates:` 和 `finalizeCollectionViewUpdates`，这样我们只对插入或删除的项目进行动画。我们将创建两个可变集，以挂在正在插入或删除的项目上。

我们将为单元格添加一个淡入淡出的动画，并使它们旋转。我希望它们顺时针旋转，所以插入前的初始旋转将是 -90º，删除后的最终旋转将是 90º。这些都必须用弧度来指定（参见清单 5.11）。

```objc
@interface AFCollectionViewFlowLayout ()

@property (nonatomic, strong) NSMutableSet *insertedRowSet;
@property (nonatomic, strong) NSMutableSet *deletedRowSet;

@end

@implementation AFCollectionViewFlowLayout

-(id)init {
    if (!(self = [super init])) return nil;
    
    self.itemSize = CGSizeMake(200, 200);
    self.sectionInset = UIEdgeInsetsMake(13.0f, 13.0f, 13.0f, 13.0f);
    self.minimumInteritemSpacing = 13.0f;
    self.minimumLineSpacing = 13.0f;
    
    // Must instantiate these in init or else they'll always be empty
    self.insertedRowSet = [NSMutableSet set];
    self.deletedRowSet = [NSMutableSet set];
    
    return self;
}

#pragma mark - Overridden Methods

-(void)prepareForCollectionViewUpdates:(NSArray *)updateItems
{
    [super prepareForCollectionViewUpdates:updateItems];
    
    [updateItems enumerateObjectsUsingBlock:^(UICollectionViewUpdateItem *updateItem, NSUInteger idx, BOOL *stop) {
        if (updateItem.updateAction == UICollectionUpdateActionInsert) {
            [self.insertedRowSet addObject:@(updateItem.indexPathAfterUpdate.item)];
        } else if (updateItem.updateAction == UICollectionUpdateActionDelete) {
            [self.deletedRowSet addObject:@(updateItem.indexPathBeforeUpdate.item)];
        }
    }];
}

-(void)finalizeCollectionViewUpdates
{
    [super finalizeCollectionViewUpdates];
    
    [self.insertedRowSet removeAllObjects];
    [self.deletedRowSet removeAllObjects];
}

- (UICollectionViewLayoutAttributes *)initialLayoutAttributesForAppearingItemAtIndexPath:(NSIndexPath *)itemIndexPath
{
    UICollectionViewLayoutAttributes *attributes = [super initialLayoutAttributesForAppearingItemAtIndexPath:itemIndexPath];
    
    if ([self.insertedRowSet containsObject:@(itemIndexPath.item)]) {
        attributes = [self layoutAttributesForItemAtIndexPath:itemIndexPath];
        attributes.alpha = 0.0;
        attributes.transform3D = CATransform3DMakeScale(0.1, 0.1, 1.0);
        attributes.transform3D = CATransform3DRotate(attributes.transform3D, -M_PI_4, 0, 0, 1);
        return attributes;
    }
    
    return attributes;
}

- (UICollectionViewLayoutAttributes *)finalLayoutAttributesForDisappearingItemAtIndexPath:(NSIndexPath *)itemIndexPath
{
    // The documentation says that this returns nil. It is lying.
    UICollectionViewLayoutAttributes *attributes = [super finalLayoutAttributesForDisappearingItemAtIndexPath:itemIndexPath];
    
    if ([self.deletedRowSet containsObject:@(itemIndexPath.item)]) {
        attributes = [self layoutAttributesForItemAtIndexPath:itemIndexPath];
        attributes.alpha = 0.0;
        attributes.transform3D = CATransform3DMakeScale(0.1, 0.1, 1.0);
        attributes.transform3D = CATransform3DRotate(attributes.transform3D, M_PI_4, 0, 0, 1);
        
        return attributes;
    }
    
    return attributes;
}

@end

```

让我们添加代码来插入和删除单元格，如清单5.12所示。

```objc
-(void)addItem
{
    [self.collectionView performBatchUpdates:^{
        self.cellCount = self.cellCount + 1;
        [self.collectionView insertItemsAtIndexPaths:[NSArray arrayWithObject:[NSIndexPath indexPathForItem:self.cellCount-1 inSection:0]]];
    } completion:nil];
}

-(void)deleteItem
{
    // Always have at least once cell in our collection view
    if (self.cellCount == 1) return;
    
    [self.collectionView performBatchUpdates:^{
        self.cellCount = self.cellCount - 1;
        [self.collectionView deleteItemsAtIndexPaths:[NSArray arrayWithObject:[NSIndexPath indexPathForItem:self.cellCount inSection:0]]];
    } completion:nil];
}

```

这就是插入和删除动画的完整实现。请注意，我们不需要自己做任何自定义的动画工作，我们只需要覆盖现有的方法并设置现有的属性。

在插入和删除动画中，属性的 `alpha` 值均为 0。`transform3D` 属性用于在每个动画中顺时针旋转单元格四分之一弧度（90º）。此外，我们还将单元格缩小到其通常尺寸的 10%。我们做这些操作的顺序通常很重要，但在本例中并不重要。

我们连接变换的顺序通常是很重要的，因为 `CATrasform3D` 是没有交流的。连结变换使用的是后序乘法；所以如果你想要一个缩放，然后是一个转换，你需要将缩放变换与转换变换连在一起。始终以您希望应用的相反顺序应用变换。请参阅图 5.4，了解我们正在运行的带有插入/删除动画的应用程序。

在圆圈布局类中，添加相同的 `insertedRowSet` 和 `deleteRowSet` 私有属性，并在 `init` 中实例化它们。同时为`prepareForCollectionViewUpdates:` 和 `finalizeCollectionViewUpdates` 编写相同的实现，我不会在清单5.13中包含。

```objc
- (UICollectionViewLayoutAttributes *)initialLayoutAttributesForAppearingItemAtIndexPath:(NSIndexPath *)itemIndexPath
{
    UICollectionViewLayoutAttributes *attributes = [super initialLayoutAttributesForAppearingItemAtIndexPath:itemIndexPath];
    
    if ([self.insertedRowSet containsObject:@(itemIndexPath.item)])
    {
        attributes = [self layoutAttributesForItemAtIndexPath:itemIndexPath];
        attributes.alpha = 0.0;
        attributes.center = self.center;
        return attributes;
    }
    
    return attributes;
}

- (UICollectionViewLayoutAttributes *)finalLayoutAttributesForDisappearingItemAtIndexPath:(NSIndexPath *)itemIndexPath
{
    // The documentation says that this returns nil. It is lying. 
    UICollectionViewLayoutAttributes *attributes = [super finalLayoutAttributesForDisappearingItemAtIndexPath:itemIndexPath];
    
    if ([self.deletedRowSet containsObject:@(itemIndexPath.item)])
    {
        attributes = [self layoutAttributesForItemAtIndexPath:itemIndexPath];
        attributes.alpha = 0.0;
        attributes.center = self.center;
        attributes.transform3D = CATransform3DConcat(CATransform3DMakeRotation((2 * M_PI * itemIndexPath.item / (self.cellCount + 1)), 0, 0, 1), CATransform3DMakeScale(0.1, 0.1, 1.0));
        
        return attributes;
    }
    
    return attributes;
}
```

你可以看到，我们对插入和删除应用了几乎相同的动画。

插入和删除动画之间的一个区别是旋转。对于插入，我们在调用 `layoutAttributesForItemAtIndexPath:` 时，除了已经计算好的之外，并没有指定一个。这对于删除是行不通的，原因很微妙。在调用`initialLayoutAttributesForAppearingItemAtIndexPath:` 或 `finalLayoutAttributesForDisappearingItemAtIndexPath:` 时，`cellCount` 属性已经更新了。当插入时，这意味着在 `layoutAttributesForItemAtIndexPath:` 中计算出的旋转角度反映了新单元格数的正确角度，这就是我们想要的。然而，当删除时，我们不希望单元格有反映新单元格数的更新角度；我们希望它有它的旧角度。这意味着我们需要重新计算旋转角度。

我们将两个 3D 变换合并在一起：将比例缩小到项目大小的 10%，并使用旧的 `cellCount` 计算旋转角度：`cellCount + 1`。同样，在这种情况下，变换的顺序并不重要。请看图5.5中我们的新动画。

到目前为止，我们已经得到了相当不错的东西；我们正在动画化所有可以动画化的东西。让我们在圆圈布局的中心添加一个装饰视图，在给定当前时间的情况下，它将指向分针所指向的相同位置。这将提醒我们如何实现装饰视图，并告诉你你使用与UICollectionViewFlowLayout相同的方法。

首先，让我们实现装饰视图类（见清单5.14）。回想一下，任何装饰视图都必须是 `UICollectionReusableView` 的子类。

```objc
#import "AFDecorationView.h"

@implementation AFDecorationView

- (id)initWithFrame:(CGRect)frame
{
    if (!(self = [super initWithFrame:frame])) return nil;
    
    self.backgroundColor = [UIColor whiteColor];
    
    CAGradientLayer *gradientLayer = [CAGradientLayer layer];
    gradientLayer.colors = @[(id)[[UIColor blackColor] CGColor], (id)[[UIColor clearColor] CGColor]];
    gradientLayer.backgroundColor = [[UIColor clearColor] CGColor];
    gradientLayer.frame = self.bounds;
    
    self.layer.mask = gradientLayer;
    
    self.autoresizingMask = UIViewAutoresizingFlexibleBottomMargin | UIViewAutoresizingFlexibleTopMargin | UIViewAutoresizingFlexibleLeftMargin | UIViewAutoresizingFlexibleRightMargin;
    
    return self;
}

-(void)layoutSubviews
{
    [super layoutSubviews];
    
    CAGradientLayer *gradientLayer = [CAGradientLayer layer];
    gradientLayer.colors = @[(id)[[UIColor blackColor] CGColor], (id)[[UIColor clearColor] CGColor]];
    gradientLayer.backgroundColor = [[UIColor clearColor] CGColor];
    gradientLayer.frame = self.bounds;
    
    self.layer.mask = gradientLayer;
}

@end
```

我们创建一个渐变蒙板，它跨越装饰视图的长度，这样我们就可以分辨出哪边是哪边。它看起来也很酷，但你不希望过多地使用 `CALayer` 的 `mask` 属性，因为它会减慢视图的渲染速度。

接下来，我们需要在圆圈布局的 `init` 方法中注册装饰视图类（见清单5.15）。

```
-(id)init
{
    if (!(self = [super init])) return nil;
    
    self.insertedRowSet = [NSMutableSet set];
    self.deletedRowSet = [NSMutableSet set];
    
    // 注册重用装饰视图
    [self registerClass:[AFDecorationView class] forDecorationViewOfKind:AFCollectionViewFlowDecoration];
    
    return self;
}
```

为了显示我们的装饰视图，我们需要在我们的 `layoutAttributesForElementsInRect:` 实现代码中的`UICollectionViewLayoutAttributes` 对象中添加一个装饰视图。（见清单5.16）。

```objc
-(NSArray *)layoutAttributesForElementsInRect:(CGRect)rect
{
    NSMutableArray* attributes = [NSMutableArray array];
    
    for (NSInteger i = 0 ; i < self.cellCount; i++)
    {
        NSIndexPath* indexPath = [NSIndexPath indexPathForItem:i inSection:0];
        [attributes addObject:[self layoutAttributesForItemAtIndexPath:indexPath]];
    }
    
    // 添加装饰视图
    if (CGRectContainsPoint(rect, self.center))
    {
        [attributes addObject:[self layoutAttributesForDecorationViewOfKind:AFCollectionViewFlowDecoration atIndexPath:[NSIndexPath indexPathForItem:0 inSection:0]]];
    }
    
    return attributes;
}
```

确保矩形包含中心点的检查可能是多余的，但仍然是很好的做法。现在我们已经将装饰视图添加到集合视图中，我们需要给它适当的变换，如清单5.17所示。

```objc
// 实现装饰视图布局方法
-(UICollectionViewLayoutAttributes *)layoutAttributesForDecorationViewOfKind:(NSString *)decorationViewKind atIndexPath:(NSIndexPath *)indexPath
{
    UICollectionViewLayoutAttributes *layoutAttributes = [UICollectionViewLayoutAttributes layoutAttributesForDecorationViewOfKind:decorationViewKind withIndexPath:indexPath];

    if ([decorationViewKind isEqualToString:AFCollectionViewFlowDecoration])
    {
        CGFloat rotationAngle = 0.0f;
        
        if ([self.collectionView.delegate conformsToProtocol:@protocol(AFCollectionViewDelegateCircleLayout) ])
        {
            rotationAngle = [(id<AFCollectionViewDelegateCircleLayout>)self.collectionView.delegate rotationAngleForSupplmentaryViewInCircleLayout:self];
        }
        
        layoutAttributes.size = CGSizeMake(20, 200);
        layoutAttributes.center = self.center;
        layoutAttributes.transform3D = CATransform3DMakeRotation(rotationAngle, 0, 0, 1);
        // Place the decoration view behind all the cells
        layoutAttributes.zIndex = -1;
    }
    
    return layoutAttributes;
}
```

我已经创建了一个 `AFCollectionViewDelegateCircleLayout` 协议，我们用它来查询集合视图的委托人，以确定我们应该使用的旋转（见清单5.18）。

```objc
-(CGFloat)rotationAngleForSupplmentaryViewInCircleLayout:(AFCollectionViewCircleLayout *)circleLayout
{
    CGFloat timeRatio = 0.0f;
    
    NSDate *date = [NSDate date];
    NSDateComponents *components = [[NSCalendar currentCalendar] components:NSMinuteCalendarUnit fromDate:date];
    timeRatio = (CGFloat)(components.minute) / 60.0f;
    
    return (2 * M_PI * timeRatio);
}
```

这是一个简单的实现，它可以抓取当前的分钟，做出每个小时只有60分钟的假设（形式不好，我知道），并计算出模拟钟分针的当前角度，如图5.6所示。

在iOS 6中，装饰视图因不可靠而有些臭名昭著，尤其是在旋转动画中。我在2013年WWDC上与一些苹果工程师进行了交流，能够让他们注意到一些边缘案例。

## 堆栈布局

让我们把所学到的东西结合在一起，做一个像真正的 app 一样的东西。当我为 500px 工作的时候，我写了他们的开源 iOS SDK，现在我们将用它来制作一个基本的应用程序来显示他们网站上的图片。我们还将使用我编写的图片下载器来下载图片，一旦我们从 500px API 中检索到 URL，就可以下载图片。这个项目的示例代码叫做 One Hundred Pixels，因为这大约是任何真正的 500px 应用的五分之一。

首先，你需要在 500px 应用程序上注册一个应用。这将让你得到一个用户密钥和秘密对，你需要用它来对 API 请求进行签名。创建一个新的应用程序，并在 Xcode 项目中包含 500px iOS SDK 和AFImageDownloader 类。将这些`#import`导入到你的预编译头中，并在 `applicationDidFinishLaucningWithOptions:` 方法中设置`PXRequest` 类（见清单5.19）。

```objc
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];
    // Override point for customization after application launch.
    self.viewController = [[UINavigationController alloc] initWithRootViewController:[[AFViewController alloc] init]];
    self.viewController.navigationBar.barStyle = UIBarStyleBlack;
    self.window.rootViewController = self.viewController;
    [self.window makeKeyAndVisible];
    
    [PXRequest setConsumerKey:@"MKGcPSdunLc5ZplYdIkY8uEc3x8zMDgn73UIMwTb" consumerSecret:@"Go register your own app at http://500px.com/settings/applications"];
    
    return YES;
}
```

...